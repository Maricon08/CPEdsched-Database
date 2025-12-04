# credentials Table

## Purpose
Stores student credential verification records in the PUP College of Engineering Computer Engineering Laboratory system. This table manages the Certificate of Registration (COR) upload and verification process, tracking document submissions, approval status, expiration dates, and admin verification history. It ensures that only verified students with valid enrollment can access laboratory facilities and equipment.

---

## Table Structure

| Column Name       | Data Type       | Constraints                                               | Description                                                                     |
|------------------|----------------|-----------------------------------------------------------|---------------------------------------------------------------------------------|
| credential_id     | INT            | PRIMARY KEY, AUTO_INCREMENT, NOT NULL                    | Unique identifier for each credential submission record                         |
| credential_file   | VARCHAR(255)  | NOT NULL                                                  | Filename or file path of the uploaded COR document (PDF)                       |
| c_created         | DATETIME       | NOT NULL, DEFAULT CURRENT_TIMESTAMP                       | Timestamp when the credential was uploaded by the student                       |
| c_expired         | DATE           | NOT NULL                                                  | Expiration date of the COR (6 months from verification date)                    |
| c_status          | ENUM('Pending','Expired','Verified','Rejected') | NOT NULL, DEFAULT 'Pending' | Current verification status of the credential                                    |
| verified_by       | INT            | FOREIGN KEY, NULL                                         | References admin_id of the administrator who verified or rejected the credential |

---

## Key Features

### Document Management
- Tracks uploaded COR files for student submissions.
- Stores file references for retrieval and verification.
- Maintains upload timestamps for audit purposes.

### Verification Workflow
- **Pending**: Initial status after student upload.
- **Verified**: Approved by admin.
- **Rejected**: Marked invalid by admin.
- **Expired**: Credential is past its 6-month validity.

### Expiration Tracking
- COR valid for 6 months after verification.
- System blocks access once expired.

### Admin Accountability
- `verified_by` keeps record of the admin who reviewed the credential.
- Supports accountability and audit trail.

### Temporal Tracking
- Tracks submission time and verification history.

---

## Relationships

### Parent Table
**admin_data**
- credentials.verified_by → admin_data.admin_id  
- Tracks which admin processed the credential.

### Referenced By
**student_data**
- student_data.credential_id → credentials.credential_id  
- Each student has one active credential record.

---

## System Workflow

Student registers
↓
Uploads COR
↓
credentials created (status = Pending)
↓
student_data.credential_id updated
↓
Admin reviews credential
↓
Approved → status = Verified → c_expired = +6 months → can reserve
Rejected → status = Rejected → must resubmit
Expired → status changes automatically or via system check


---

## Table Notes

1. Initial credential is always **Pending**.
2. Verified credential unlocks system access (reservations).
3. Rejected and expired credentials block access.
4. Admin review required for every document.
5. Multiple historical credentials retained for audit trail.

---

## Status Transitions

| From        | To         | Reason |
|-------------|------------|--------|
| Pending     | Verified   | Valid COR approved |
| Pending     | Rejected   | Invalid COR |
| Verified    | Expired    | After 6 months |
| Rejected    | New Record | Student resubmits |

---

## File Storage Notes
- `credential_file` stores filename or path.
- Files stored on server directory.
- Suggested naming format: `COR_studentID_timestamp.pdf`.
- Acceptable formats: PDF, JPG, PNG.

---

## Sample Data

### Example 1 (Verified)


credential_id: 1
credential_file: COR_2021-12345-MN-0_20240815.pdf
c_created: 2024-08-15 09:30:00
c_expired: 2025-02-15
c_status: Verified
verified_by: 2


### Example 2 (Pending)


credential_id: 2
credential_file: COR_2022-23456-MN-0_20240816.pdf
c_created: 2024-08-16 10:45:00
c_expired: (tentative)
c_status: Pending
verified_by: NULL


---

## Foreign Key Configuration

| Foreign Key Name           | Referenced Table | Column      | Referenced Column |
|---------------------------|------------------|-------------|-------------------|
| fk_credential_verified_by | admin_data       | verified_by | admin_id          |

**Recommended settings:**  
- ON DELETE: SET NULL  
- ON UPDATE: CASCADE  

---

## Data Validation Rules

1. `credential_file` must not be NULL.
2. `c_expired` must be verification_date + 6 months.
3. Status must be one of: Pending, Verified, Rejected, Expired.
4. verified_by:
   - NULL for Pending
   - Must reference admin when Verified or Rejected
5. Only Verified status grants reservation access.

---

## SQL Code

```sql
CREATE TABLE credentials (
    credential_id INT PRIMARY KEY AUTO_INCREMENT NOT NULL,
    credential_file VARCHAR(255) NOT NULL,
    c_created DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    c_expired DATE NOT NULL,
    c_status ENUM('Pending', 'Expired', 'Verified', 'Rejected') NOT NULL DEFAULT 'Pending',
    verified_by INT NULL,
    
    CONSTRAINT fk_credential_verified_by
        FOREIGN KEY (verified_by)
        REFERENCES admin_data(admin_id)
        ON DELETE SET NULL
        ON UPDATE CASCADE
);

End of credentials documentation


---

If you want, send the next table and I’ll convert it cleanly too. I’ll pretend this isn’t a small mountain of documentation waiting for me.
