# Event Ticketing System Requirements

## 1. General Information
- Project Name: Event Ticketing Management System
- Objective: Build an online event ticketing management system that provides functionalities for event management, ticket booking, and booked ticket management.

## 2. Functional Requirements

### 2.1 Authentication & Authorization
- User Registration: Users can create new accounts with email and password. Required fields: Full name, Email, Password, Phone number. Email verification required. Password must meet security requirements (min 8 chars, including numbers and special characters).
- User Login: Login using email and password. JWT token-based authentication. Token refresh mechanism. Password reset functionality.
- User Roles: Admin: Full system access. Event Manager: Event management access. Customer: Basic user access.

### 2.2 Event Management
- Add Event: Allows administrators to add new event information, including event name, description, date and time, venue, number of tickets, ticket prices, and event images.
- Create Ticket Types: Allows administrators to create different ticket types for an event, each with its own quantity and price.
- Edit Event: Allows administrators to edit existing event information.
- Delete Event: Allows administrators to delete events from the system.
- View Events: Allows administrators to view a list of all events.

### 2.3 Ticket Booking
- View Events: Users can view a list of available events, including detailed information about each event.
- Book Tickets: Users can select an event and book tickets. The system will require personal information and payment method.
- Ticket Selection: Users can select ticket type and quantity (no seat selection required).

### 2.4 Booked Ticket Management
- View Booked Tickets: Users can view information about booked tickets, including ticket code, event information, and payment status.
- Payment Status: The system updates the payment status of tickets (paid, unpaid, canceled).
- Group Tickets by Event: Users can view booked tickets grouped by event, with QR codes for each ticket.
- Event Reminder: The system can send reminders when the event is approaching.

### 2.5 Payment Process
- Payment Methods: Support multiple payment methods: Credit Card, Bank Transfer, Digital Wallet.
- Payment Confirmation: Confirm payment and generate receipt.
- Refund Processing: Process refunds for cancellations.
- Ticket Reservation: When proceeding to payment, tickets are held for 10 minutes. If the payment is not completed within this time, the tickets are released back to the pool.

## 3. Admin Dashboard
- Event Analytics: Total tickets sold, Revenue generated, Popular events, Sales trends.
- User Management: View all users, Modify user roles, Block/Unblock users, View user activity.
- Reports: Sales reports, Event performance reports, User activity reports, Revenue reports.

## 4. Technical Requirements
- Backend: ASP.NET Core Web API, Entity Framework Core for database operations, SQL Server database, Redis for caching, JWT authentication, RESTful API design, API documentation with Swagger.
- Security: Token-based authentication, Password encryption, Input validation, XSS protection, CSRF protection, Rate limiting.
- Performance: Response time < 2 seconds, Support for concurrent users, Caching implementation, Database optimization.
- Deployment: Docker containerization, Environment configuration, Logging system, Error handling, Database backup.

## 5. API Endpoints
### 5.1 Authentication
- POST /api/auth/register
- POST /api/auth/login
- POST /api/auth/refresh-token
- POST /api/auth/forgot-password

### 5.2 Events
- GET /api/events
- GET /api/events/{id}
- POST /api/events
- PUT /api/events/{id}
- DELETE /api/events/{id}

### 5.3 Tickets
- GET /api/tickets
- POST /api/tickets/book
- GET /api/tickets/{id}
- PUT /api/tickets/{id}/cancel

### 5.4 Payments
- POST /api/payments/process
- GET /api/payments/{id}
- POST /api/payments/{id}/refund

### 5.5 Admin
- GET /api/admin/users
- GET /api/admin/reports
- GET /api/admin/statistics

## 6. Database Design

### 6.1 Users Table
| Column Name   | Data Type     | Constraint                | Description                              |
|---------------|---------------|---------------------------|------------------------------------------|
| UserId        | INT           | PK, NOT NULL, IDENTITY    | Khóa chính, tự tăng                     |
| Email         | NVARCHAR(255) | NOT NULL, UNIQUE          | Email người dùng, duy nhất               |
| PasswordHash  | NVARCHAR(MAX) | NOT NULL                  | Mật khẩu đã mã hóa                      |
| FullName      | NVARCHAR(255) | NOT NULL                  | Họ và tên người dùng                    |
| PhoneNumber   | NVARCHAR(20)  | NULL                      | Số điện thoại người dùng                |
| CreatedAt     | DATETIME      | NOT NULL, DEFAULT(GETDATE()) | Ngày tạo tài khoản                       |
| UpdatedAt     | DATETIME      | NULL                      | Ngày cập nhật tài khoản                 |
| LastLoginAt   | DATETIME      | NULL                      | Ngày đăng nhập cuối cùng                |
| Status        | NVARCHAR(50)  | NOT NULL, DEFAULT('Active') | Trạng thái tài khoản (Active/Inactive)   |

### 6.2 Roles Table
| Column Name   | Data Type     | Constraint                | Description                              |
|---------------|---------------|---------------------------|------------------------------------------|
| RoleId        | INT           | PK, NOT NULL, IDENTITY    | Khóa chính, tự tăng                     |
| Name          | NVARCHAR(50)  | NOT NULL, UNIQUE          | Tên vai trò (Admin/EventManager/Customer)|
| Description   | NVARCHAR(255) | NULL                      | Mô tả vai trò                            |
| CreatedAt     | DATETIME      | NOT NULL, DEFAULT(GETDATE()) | Ngày tạo vai trò                         |
| UpdatedAt     | DATETIME      | NULL                      | Ngày cập nhật vai trò                   |

### 6.3 UserRoles Table
| Column Name   | Data Type     | Constraint                | Description                              |
|---------------|---------------|---------------------------|------------------------------------------|
| UserRoleId    | INT           | PK, NOT NULL, IDENTITY    | Khóa chính, tự tăng                     |
| UserId        | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu Users(UserId)    |
| RoleId        | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu Roles(RoleId)    |
| AssignedAt    | DATETIME      | NOT NULL, DEFAULT(GETDATE()) | Ngày gán vai trò                         |
| AssignedBy    | INT           | NULL                      | Người gán vai trò                       |

### 6.4 Events Table
| Column Name   | Data Type     | Constraint                | Description                              |
|---------------|---------------|---------------------------|------------------------------------------|
| EventId       | INT           | PK, NOT NULL, IDENTITY    | Khóa chính, tự tăng                     |
| Name          | NVARCHAR(255) | NOT NULL                  | Tên sự kiện                              |
| Description   | NVARCHAR(MAX) | NULL                      | Mô tả sự kiện                            |
| StartDate     | DATETIME      | NOT NULL                  | Ngày bắt đầu sự kiện                     |
| EndDate       | DATETIME      | NOT NULL                  | Ngày kết thúc sự kiện                    |
| VenueName     | NVARCHAR(255) | NOT NULL                  | Tên địa điểm                             |
| VenueAddress  | NVARCHAR(255) | NOT NULL                  | Địa chỉ địa điểm                         |
| Category      | NVARCHAR(50)  | NULL                      | Thể loại sự kiện                         |
| ArtistInfo    | NVARCHAR(255) | NULL                      | Thông tin nghệ sĩ                        |
| ImageUrls     | NVARCHAR(MAX) | NULL                      | URL hình ảnh sự kiện                     |
| TotalTickets  | INT           | NOT NULL                  | Tổng số vé                               |
| RemainingTickets | INT        | NOT NULL                  | Số vé còn lại                            |
| Status        | NVARCHAR(50)  | NOT NULL, DEFAULT('Upcoming') | Trạng thái sự kiện (Upcoming/Ongoing/Completed/Cancelled) |
| CreatedBy     | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu Users(UserId)    |
| CreatedAt     | DATETIME      | NOT NULL, DEFAULT(GETDATE()) | Ngày tạo sự kiện                         |
| UpdatedAt     | DATETIME      | NULL                      | Ngày cập nhật sự kiện                   |

### 6.5 TicketTypes Table
| Column Name   | Data Type     | Constraint                | Description                              |
|---------------|---------------|---------------------------|------------------------------------------|
| TicketTypeId  | INT           | PK, NOT NULL, IDENTITY    | Khóa chính, tự tăng                     |
| EventId       | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu Events(EventId)  |
| Name          | NVARCHAR(50)  | NOT NULL                  | Tên hạng vé (VIP/Standard/Economy)       |
| Price         | DECIMAL(18, 2)| NOT NULL                  | Giá vé                                   |
| TotalQuantity | INT           | NOT NULL                  | Tổng số lượng vé                         |
| RemainingQuantity | INT       | NOT NULL                  | Số lượng vé còn lại                      |
| Description   | NVARCHAR(255) | NULL                      | Mô tả hạng vé                            |
| MaxPerCustomer| INT           | NOT NULL                  | Số lượng vé tối đa mỗi khách hàng        |
| SaleStartDate | DATETIME      | NOT NULL                  | Ngày bắt đầu bán vé                      |
| SaleEndDate   | DATETIME      | NOT NULL                  | Ngày kết thúc bán vé                     |
| CreatedAt     | DATETIME      | NOT NULL, DEFAULT(GETDATE()) | Ngày tạo hạng vé                         |
| UpdatedAt     | DATETIME      | NULL                      | Ngày cập nhật hạng vé                   |
| Status        | NVARCHAR(50)  | NOT NULL, DEFAULT('Active') | Trạng thái hạng vé (Active/Inactive)     |

### 6.6 Tickets Table
| Column Name   | Data Type     | Constraint                | Description                              |
|---------------|---------------|---------------------------|------------------------------------------|
| TicketId      | INT           | PK, NOT NULL, IDENTITY    | Khóa chính, tự tăng                     |
| BookingId     | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu Bookings(BookingId)|
| TicketTypeId  | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu TicketTypes(TicketTypeId)|
| TicketNumber  | NVARCHAR(50)  | NOT NULL, UNIQUE          | Mã vé, duy nhất                          |
| Status        | NVARCHAR(50)  | NOT NULL, DEFAULT('Reserved') | Trạng thái vé (Reserved/Confirmed/Cancelled) |
| ReservedAt    | DATETIME      | NULL                      | Ngày giữ vé                              |
| ConfirmedAt   | DATETIME      | NULL                      | Ngày xác nhận vé                         |
| CreatedAt     | DATETIME      | NOT NULL, DEFAULT(GETDATE()) | Ngày tạo vé                              |

### 6.7 Bookings Table
| Column Name   | Data Type     | Constraint                | Description                              |
|---------------|---------------|---------------------------|------------------------------------------|
| BookingId     | INT           | PK, NOT NULL, IDENTITY    | Khóa chính, tự tăng                     |
| UserId        | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu Users(UserId)    |
| EventId       | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu Events(EventId)  |
| TotalAmount   | DECIMAL(18, 2)| NOT NULL                  | Tổng số tiền                             |
| Status        | NVARCHAR(50)  | NOT NULL, DEFAULT('Pending') | Trạng thái đặt vé (Pending/Confirmed/Cancelled) |
| BookingDate   | DATETIME      | NOT NULL, DEFAULT(GETDATE()) | Ngày đặt vé                              |
| ExpiryDate    | DATETIME      | NULL                      | Ngày hết hạn đặt vé                      |
| CreatedAt     | DATETIME      | NOT NULL, DEFAULT(GETDATE()) | Ngày tạo đặt vé                          |
| UpdatedAt     | DATETIME      | NULL                      | Ngày cập nhật đặt vé                    |

### 6.8 BookingDetails Table
| Column Name   | Data Type     | Constraint                | Description                              |
|---------------|---------------|---------------------------|------------------------------------------|
| BookingDetailId | INT         | PK, NOT NULL, IDENTITY    | Khóa chính, tự tăng                     |
| BookingId     | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu Bookings(BookingId)|
| TicketTypeId  | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu TicketTypes(TicketTypeId)|
| Quantity      | INT           | NOT NULL                  | Số lượng vé                              |
| UnitPrice     | DECIMAL(18, 2)| NOT NULL                  | Giá vé đơn                               |
| Subtotal      | DECIMAL(18, 2)| NOT NULL                  | Tổng tiền                                |

### 6.9 Payments Table
| Column Name   | Data Type     | Constraint                | Description                              |
|---------------|---------------|---------------------------|------------------------------------------|
| PaymentId     | INT           | PK, NOT NULL, IDENTITY    | Khóa chính, tự tăng                     |
| BookingId     | INT           | FK, NOT NULL              | Khóa ngoại, tham chiếu Bookings(BookingId)|
| Amount        | DECIMAL(18, 2)| NOT NULL                  | Số tiền thanh toán                       |
| PaymentMethod | NVARCHAR(50)  | NOT NULL                  | Phương thức thanh toán                   |
| Status        | NVARCHAR(50)  | NOT NULL, DEFAULT('Pending') | Trạng thái thanh toán (Pending/Completed/Failed/Refunded) |
| TransactionId | NVARCHAR(50)  | NULL                      | Mã giao dịch                             |
| PaymentDate   | DATETIME      | NULL                      | Ngày thanh toán                          |
| RefundDate    | DATETIME      | NULL                      | Ngày hoàn tiền                           |
| CreatedAt     | DATETIME      | NOT NULL, DEFAULT(GETDATE()) | Ngày tạo thanh toán                      |
| UpdatedAt     | DATETIME      | NULL                      | Ngày cập nhật thanh toán                |
