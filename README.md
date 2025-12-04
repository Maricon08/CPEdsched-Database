# admin_data — Table Information

## 1. Purpose
The `admin_data` table stores administrator accounts for managing the CPE Laboratory reservation and equipment system. It includes admin identity details, login credentials, security settings, and timestamps for auditing.

---

## 2. Table Structure

| Column Name         | Data Type     | Constraints                                                             | Description                                           |
|---------------------|---------------|-------------------------------------------------------------------------|-------------------------------------------------------|
| admin_id            | INT           | PRIMARY KEY, AUTO_INCREMENT, NOT NULL                                   | Unique ID of each administrator                       |
| a_fname             | VARCHAR(100)  | NOT NULL                                                                | First name of the admin                               |
| a_mname             | VARCHAR(100)  | NOT NULL                                                                | Middle name of the admin                              |
| a_sname             | VARCHAR(100)  | NOT NULL                                                                | Last name of the admin                                |
| a_role              | VARCHAR(45)   | NOT NULL                                                                | Role of admin (`Super Admin`, `Lab Admin`)            |
| webmail             | VARCHAR(255)  | UNIQUE, NOT NULL                                                        | Institutional email used for login                    |
| password            | VARCHAR(255)  | NOT NULL                                                                | Hashed password                                       |
| account_status      | ENUM('Active','Inactive') | NOT NULL, DEFAULT 'Active'                                  | Login availability                                     |
| lastlog             | DATETIME      | NULL                                                                   | Last login/logout timestamp                           |
| created_at          | DATETIME      | NOT NULL, DEFAULT CURRENT_TIMESTAMP                                     | Record creation timestamp                              |
| update_at           | DATETIME      | NOT NULL, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP         | Last update timestamp                                  |
| fail_attempts       | INT           | NOT NULL, DEFAULT 0                                                    | Failed login attempts counter                         |
| locked_account      | TINYINT       | NOT NULL, DEFAULT 0                                                    | 0 = unlocked, 1 = locked                              |
| last_failed_login   | DATETIME      | NULL                                                                   | Timestamp of last failed login                        |

---

## 3. Key Features

### 🔐 Security
- Tracks failed login attempts  
- Auto-locks after **3 failed attempts**  
- Requires Super Admin to unlock  
- Passwords stored in hashed form  
- Logs login activity  

### 👤 Account Management
- Role-based access: Super Admin vs Lab Admin  
- Activation and deactivation of accounts  
- Audit logs for updates and login events  
- Email-based authentication  

---

## 4. Role Types

### **Super Admin**
- Full control  
- Can add/modify/delete admin accounts  
- Can delete system data  
- Can unlock accounts  
- Can access settings and reports  

### **Lab Admin**
- Daily lab operations  
- Cannot delete accounts  
- Cannot modify admin accounts  
- Limited to create/view/update  

---

## 5. Table Notes

### 🔒 Account Lock Rules
- 3 failed attempts → `locked_account = 1`  
- Only Super Admin can unlock  
- Successful login resets fail count  

### 📧 Email Rules
- Must be `@pup.edu.ph` institutional email  
- Must be unique  

---

## 6. Relationships

| Table & Column                       | Relationship                            |
|-------------------------------------|------------------------------------------|
| credentials.verified_by             | FK → admin_data.admin_id                 |
| reservation.notified_by             | FK → admin_data.admin_id                 |
| reservation.cancelled_by_admin_id   | FK → admin_data.admin_id                 |
| equipment_return.verified_by        | FK → admin_data.admin_id                 |

---

