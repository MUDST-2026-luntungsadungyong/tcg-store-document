# 🔔 09: ระบบบริการแจ้งเตือนผู้ใช้งาน (Notification Delivery Service)
**โฟลเดอร์ปลายทาง:** `tcg-store-backend/src/main/java/com/mythix/tcg/notification`  
**เอกสารอ้างอิง:** [⬅️ กลับสู่สารบัญ Backend](../README.md) | [🏠 หน้าหลักสารบัญ Code](../../README.md)

---

## 📌 บทสรุปและหน้าที่ของส่วนนี้ (Overview & Responsibilities)
โมดูลนี้ทำหน้าที่เป็นระบบกระจายข่าวสารและแจ้งเตือนภายในแพลตฟอร์ม (In-app Notification Hub):
1. **Centralized Notification Dispatcher (`NotificationService.java`):** เป็นจุดศูนย์กลางที่โมดูลอื่น (Order, Admin, Seller) เรียกใช้เมื่อเกิดเหตุการณ์สำคัญในระบบ
2. **Predefined System Event Types (`NotificationTypes.java`):** กำหนดประเภทแจ้งเตือนมาตรฐาน เช่น การจองออเดอร์ (`ORDER_RESERVED`), การเปลี่ยนสถานะจัดส่ง (`ORDER_STATUS_CHANGED`), และผลการอนุมัติร้านค้า (`SELLER_APPLICATION_APPROVED`)
3. **High-Performance Unread Counter:** เมธอด `countByUserIdAndReadAtIsNull` ที่ออกแบบให้ตอบสนองเร็ว สำหรับการ Polling จาก Navbar บน Frontend
4. **Mark as Read Lifecycles:** รองรับทั้งการทำเครื่องหมายว่าอ่านแล้วรายข้อความ (`markAsRead`) หรือการกดอ่านทั้งหมดในครั้งเดียว (`markAllAsRead`)

---

## 📂 ตารางโครงสร้างไฟล์และตำแหน่งที่ตั้ง (File Structure)

| ชื่อไฟล์ | ตำแหน่ง Path | หน้าที่ความรับผิดชอบ |
| :--- | :--- | :--- |
| `api/NotificationController.java` | `src/.../notification/api/NotificationController.java` | REST Endpoints: ดึงแจ้งเตือน, จำนวนที่ยังไม่อ่าน, และปรับสถานะอ่าน |
| `application/NotificationService.java` | `src/.../notification/application/NotificationService.java` | ตรรกะการสร้างข้อความแจ้งเตือน และการคำนวณ Unread Count |
| `domain/UserNotification.java` | `src/.../notification/domain/UserNotification.java` | JPA Entity บันทึกข้อความแจ้งเตือน (`title`, `message`, `type`, `readAt`) |
| `domain/NotificationTypes.java` | `src/.../notification/domain/NotificationTypes.java` | ค่าคงที่ชนิดเหตุการณ์แจ้งเตือนของระบบ |
| `persistence/NotificationRepository.java` | `src/.../notification/persistence/NotificationRepository.java` | Repository จัดการค้นหาและนับข้อความที่ `read_at IS NULL` |

---

## 🔄 แผนผังวงจรการแจ้งเตือน (Notification Flowchart)

```mermaid
flowchart TD
    EventTrigger([เหตุการณ์ในระบบ เช่น Order อนุมัติ / ปฏิเสธร้าน]) --> Service[NotificationService.dispatch...]
    Service --> CreateEntity[สร้าง UserNotification Entity<br/>กำหนด Type, Title, Message, readAt = null]
    Service --> SaveDB[บันทึกลง user_notifications]

    SaveDB --> FrontendPoll[Frontend Navbar ยิง GET /api/notifications/unread-count]
    FrontendPoll --> QueryUnread[Repository: countByUserIdAndReadAtIsNull]
    QueryUnread --> ReturnCount[ส่งตัวเลข Badge กลับไปแสดงผล]

    UserClick([ผู้ใช้กดอ่านข้อความ]) --> MarkRead[PUT /api/notifications/{id}/read]
    MarkRead --> UpdateTimestamp[บันทึก readAt = LocalDateTime.now]
    UpdateTimestamp --> DecrementBadge[ตัวเลข Badge ลดลงทันที]
```

---

## 💻 เจาะลึกโค้ดสำคัญและตัวอย่างจริง (Deep-Dive Implementation)

### 1. เซอร์วิสสร้างและจัดการแจ้งเตือน (`src/.../notification/application/NotificationService.java`)
```java
// src/main/java/com/mythix/tcg/notification/application/NotificationService.java (ส่วนสำคัญ)
package com.mythix.tcg.notification.application;

import com.mythix.tcg.notification.api.NotificationResponse;
import com.mythix.tcg.notification.domain.NotificationTypes;
import com.mythix.tcg.notification.domain.UserNotification;
import com.mythix.tcg.notification.persistence.NotificationRepository;
import com.mythix.tcg.user.domain.User;
import com.mythix.tcg.user.persistence.UserRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.time.LocalDateTime;
import java.util.List;

@Service
@RequiredArgsConstructor
public class NotificationService {

    private final NotificationRepository notificationRepository;
    private final UserRepository userRepository;

    @Transactional(readOnly = true)
    public int getUnreadCount(Long userId) {
        return (int) notificationRepository.countByUserIdAndReadAtIsNull(userId);
    }

    @Transactional(readOnly = true)
    public List<NotificationResponse> getNotifications(Long userId) {
        return notificationRepository.findByUserIdOrderByCreatedAtDesc(userId).stream()
            .map(NotificationResponse::from)
            .toList();
    }

    // เมธอดส่งแจ้งเตือนเมื่อแอดมินอนุมัติร้านค้า
    @Transactional
    public void dispatchApproved(Long userId, String storeName) {
        User user = userRepository.findById(userId).orElse(null);
        if (user == null) return;

        UserNotification notification = UserNotification.builder()
            .user(user)
            .type(NotificationTypes.SELLER_APPLICATION_APPROVED)
            .title("ยินดีด้วย! ร้านค้าของคุณได้รับการอนุมัติแล้ว")
            .message("ร้านค้า '" + storeName + "' ได้รับการอนุมัติเรียบร้อย คุณสามารถเริ่มลงขายการ์ดได้ทันที")
            .linkUrl("/workspace/listings")
            .build();

        notificationRepository.save(notification);
    }

    // ทำเครื่องหมายว่าอ่านแล้ว
    @Transactional
    public void markAsRead(Long userId, Long notificationId) {
        notificationRepository.findByIdAndUserId(notificationId, userId).ifPresent(notification -> {
            if (notification.getReadAt() == null) {
                notification.setReadAt(LocalDateTime.now());
            }
        });
    }
}
```

---

### 2. JPA Entity การแจ้งเตือน (`src/.../notification/domain/UserNotification.java`)
```java
// src/main/java/com/mythix/tcg/notification/domain/UserNotification.java
package com.mythix.tcg.notification.domain;

import com.mythix.tcg.user.domain.User;
import jakarta.persistence.*;
import lombok.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "user_notifications")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class UserNotification {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @Column(nullable = false, length = 50)
    private String type; // ดูจาก NotificationTypes

    @Column(nullable = false, length = 200)
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String message;

    @Column(name = "link_url", length = 255)
    private String linkUrl;

    @Column(name = "read_at")
    private LocalDateTime readAt;

    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @PrePersist
    protected void onCreate() {
        this.createdAt = LocalDateTime.now();
    }
}
```

---

## 🎯 จุดเด่นทางวิศวกรรม (Engineering Highlights)
* **Index-Optimized Unread Count:** คอลัมน์ `(user_id, read_at)` ถูกจัดทำ Index เพื่อให้การนับจำนวนข้อความที่ยังไม่อ่านทำได้ด้วย Index Scan รวดเร็วภายในไม่กี่ Milliseconds
* **Actionable Deep Links:** แจ้งเตือนทุกรายการสามารถผูก `linkUrl` เพื่อให้เมื่อผู้ใช้คลิกบน Frontend ระบบจะพานำทางไปยังหน้านั้นๆ ทันที (เช่น เข้าไปที่หน้ารายละเอียดออเดอร์)
* **Event-Driven Extensibility:** โครงสร้าง Service ถูกออกแบบให้พร้อมต่อยอดเป็น WebSocket (STOMP) หรือ Server-Sent Events (SSE) ได้ในอนาคตโดยไม่ต้องเปลี่ยน Domain Entity
