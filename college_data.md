# college_data — Table Information

## 1. Purpose
The `college_data` table stores information about the different colleges within the university. It serves as the top-level organizational unit in the academic hierarchy: **College → Department → Program**.

---

## 2. Table Structure

| Column Name   | Data Type     | Constraints                                  | Description                              |
|---------------|---------------|-----------------------------------------------|------------------------------------------|
| college_id    | INT           | PRIMARY KEY, AUTO_INCREMENT, NOT NULL         | Unique identifier for each college       |
| college_name  | VARCHAR(200)  | NOT NULL                                      | Full official name of the college        |

---

## 3. Key Features

### **Simple Reference Table**
- Functions as a lookup/master table for college names  
- Minimal structure for easy maintenance  
- Serves as the foundation for the academic hierarchy  

### **Data Integrity**
- Each college has a unique ID  
- College name is required (non-null)  
- Supports up to 200 characters for extended names  

---

## 4. Table Notes

1. **Unique Entries:** Each college should be listed only once.  
2. **Official Names:** Use complete, formal college names.  
3. **Cascade Impact:** Deleting a college affects departments and programs under it.  
4. **Active Reference:** Utilized throughout the system for academic filtering.  

---

## 5. Relationships

### **Parent To:**
- `departments` table  
  - `departments.college_id` → `college_data.college_id`

### **Hierarchy Flow**
college_data (College)
↓
departments (Department)
↓
program_data (Program)
↓
student_data (Students)


---

## 6. Sample Data



college_id: 1
college_name: College of Engineering

college_id: 2
college_name: College of Arts and Letters

college_id: 3
college_name: College of Science

college_id: 4
college_name: College of Computer and Information Science

college_id: 5
college_name: Institute of Technology


---

## 7. Design Notes

- **Simple by design:** Stores only essential college information  
- **Extensible:** Additional fields (e.g., `college_dean`) can be added later  
- **Performance:** Small table ensures fast lookups  
- **Maintenance:** Easy to add, edit, or update colleges  
