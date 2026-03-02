# 🍽️ Kathi Restaurant Table Booking System

A comprehensive restaurant table booking system with advanced security features, admin dashboard, and customer-facing interface. Built with PHP, MySQL, and Bootstrap.

![Version](https://img.shields.io/badge/version-2.0-blue)
![PHP](https://img.shields.io/badge/PHP-7.4+-green)
![MySQL](https://img.shields.io/badge/MySQL-5.7+-orange)
![Bootstrap](https://img.shields.io/badge/Bootstrap-4.4-purple)

## 📋 Table of Contents
- [Features](#-features)
- [Security Features](#-security-features)
- [System Requirements](#-system-requirements)
- [Installation Guide](#-installation-guide)
- [Database Setup](#-database-setup)
- [Admin Access](#-admin-access)
- [File Structure](#-file-structure)
- [Testing the Security Features](#-testing-the-security-features)
- [Troubleshooting](#-troubleshooting)
- [Screenshots](#-screenshots)

---

## ✨ Features

### Customer Features
- ✅ Online table booking with real-time availability
- ✅ Booking status checking
- ✅ Responsive design for mobile devices
- ✅ Email confirmation for bookings
- ✅ Time-based booking limits (2 bookings per 5 minutes)

### Admin Features
- ✅ Comprehensive admin dashboard
- ✅ Manage bookings (view, confirm, reject, delete)
- ✅ Table management (add, edit, delete tables)
- ✅ User management
- ✅ Contact messages viewer
- ✅ Print booking lists

---

## 🔐 Security Features

The system includes **5 unique offline security features** protecting the admin panel:

### 1. 👤 **Admin Behavior Analysis**
- Tracks login patterns (IP, time, browser)
- Detects unusual activity (new IPs, odd hours)
- Alerts for brute force attempts
- Visual login pattern charts

### 2. 🗄️ **Database Encryption & Tamper Detection**
- Encrypts sensitive data
- Creates integrity hashes for all records
- Detects unauthorized database modifications
- Tampering logs with IP tracking

### 3. 🔒 **Session Fingerprinting**
- Unique browser fingerprint for each session
- Prevents session hijacking
- Active session management
- Terminate other sessions remotely

### 4. 📊 **Audit Trail & Anomaly Detection**
- Logs all database changes (INSERT, UPDATE, DELETE)
- Detects mass deletions (10+ in 5 minutes)
- Alerts for rapid changes (20+ per minute)
- Off-hours access monitoring

### 5. 🔐 **Action Approval System**
- Two-factor approval for critical actions
- 6-digit approval codes
- 15-minute expiry window
- Pending approval dashboard

---

## 💻 System Requirements

- **Web Server**: Apache 2.4+ (XAMPP/WAMP/LAMP)
- **PHP**: Version 7.4 or higher
- **MySQL**: Version 5.7 or higher
- **Browser**: Chrome, Firefox, Edge (latest versions)
- **Storage**: 100MB minimum
- **Memory**: 256MB RAM minimum
- **OS**: Windows/Linux/MacOS

---

## 🚀 Installation Guide

### Step 1: Install XAMPP
1. Download XAMPP from [https://www.apachefriends.org](https://www.apachefriends.org)
2. Install with default settings
3. Start Apache and MySQL services

### Step 2: Download the System
```bash
# Navigate to XAMPP htdocs folder
cd C:\xampp\htdocs\

# Clone or extract files to:
# C:\xampp\htdocs\table-booking-system\
```

### Step 3: Configure Database
1. Open phpMyAdmin: `http://localhost/phpmyadmin`
2. Create new database: `kathi_db`
3. Import the database schema (see next section)

### Step 4: Configure Connection
Update database credentials if needed in:
- `connection.php` (root folder)
- `admin/partials/connection.php`

Default credentials:
```php
$server = "localhost";
$username = "root";
$password = "";
$database = "kathi_db";
```

### Step 5: Set Folder Permissions
```bash
# Create required folders with write permissions
mkdir C:\xampp\htdocs\table-booking-system\admin\includes
mkdir C:\xampp\htdocs\table-booking-system\logs
```

### Step 6: Access the System
```
Customer Site : http://localhost/table-booking-system/
Admin Panel   : http://localhost/table-booking-system/admin/
```

---

## 🗄️ Database Setup

### Create Database Tables
Run this SQL in phpMyAdmin to create all required tables:

```sql
-- Create database
CREATE DATABASE IF NOT EXISTS `kathi_db`;
USE `kathi_db`;

-- Users table
CREATE TABLE `users` (
  `id` int(21) NOT NULL AUTO_INCREMENT,
  `username` varchar(21) NOT NULL,
  `firstName` varchar(21) NOT NULL,
  `lastName` varchar(21) NOT NULL,
  `email` varchar(35) NOT NULL,
  `phone` bigint(20) NOT NULL,
  `userType` enum('0','1') NOT NULL DEFAULT '0' COMMENT '0=user, 1=admin',
  `password` varchar(255) NOT NULL,
  `joinDate` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `integrity_hash` varchar(64) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Bookings table
CREATE TABLE `tbl_booking` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `booking_id` int(11) NOT NULL,
  `name` varchar(255) NOT NULL,
  `emailid` varchar(255) NOT NULL,
  `mobile` varchar(10) NOT NULL,
  `booking_date` date NOT NULL,
  `booking_time` time NOT NULL,
  `adults` smallint(6) NOT NULL,
  `childrens` smallint(6) NOT NULL,
  `ip_address` varchar(45) DEFAULT NULL,
  `booking_status` tinyint(4) NOT NULL DEFAULT '0' COMMENT '0=New;1=Confirmed;2=Rejected',
  `integrity_hash` varchar(64) DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

-- Booking status table
CREATE TABLE `tbl_booking_status` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `booking_id` int(11) NOT NULL,
  `table_no` varchar(255) NOT NULL,
  `booking_status` varchar(255) NOT NULL COMMENT '1=Confirmed;2=Rejected',
  `remarks` text NOT NULL,
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

-- Tables table
CREATE TABLE `tbl_table` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `table_no` varchar(255) NOT NULL,
  `capacity` int(11) DEFAULT 4,
  `status` tinyint(4) DEFAULT 1,
  `integrity_hash` varchar(64) DEFAULT NULL,
  `qr_token` varchar(64) DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

-- Contact messages
CREATE TABLE `tbl_contact` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  `email` varchar(255) NOT NULL,
  `subject` varchar(255) NOT NULL,
  `message` text NOT NULL,
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

-- Security tables
CREATE TABLE `admin_behavior` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `admin_id` int(11) NOT NULL,
  `ip_address` varchar(45) DEFAULT NULL,
  `user_agent` text,
  `login_hour` int(2) DEFAULT NULL,
  `login_day` int(1) DEFAULT NULL,
  `login_date` date DEFAULT NULL,
  `login_time` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `security_alerts` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `alert_type` varchar(50) DEFAULT NULL,
  `message` text,
  `severity` enum('low','medium','high') DEFAULT 'medium',
  `is_read` tinyint(1) DEFAULT 0,
  `alert_time` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `admin_sessions` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `admin_id` int(11) NOT NULL,
  `fingerprint` varchar(64) NOT NULL,
  `ip_address` varchar(45) DEFAULT NULL,
  `user_agent` text,
  `login_time` datetime NOT NULL,
  `logout_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `data_tampering_log` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `table_name` varchar(100) NOT NULL,
  `record_id` int(11) NOT NULL,
  `expected_hash` varchar(64) NOT NULL,
  `actual_hash` varchar(64) NOT NULL,
  `detected_at` datetime NOT NULL,
  `ip_address` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `audit_log` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `admin_id` int(11) DEFAULT NULL,
  `table_name` varchar(100) NOT NULL,
  `action` varchar(50) NOT NULL,
  `record_id` int(11) NOT NULL,
  `old_value` text,
  `new_value` text NOT NULL,
  `ip_address` varchar(45) DEFAULT NULL,
  `change_time` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `pending_approvals` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `admin_id` int(11) NOT NULL,
  `action_type` varchar(50) NOT NULL,
  `action_details` text NOT NULL,
  `approval_token` varchar(64) NOT NULL,
  `approval_code` varchar(10) NOT NULL,
  `status` enum('pending','approved','rejected','expired') DEFAULT 'pending',
  `created_at` datetime NOT NULL,
  `expires_at` datetime NOT NULL,
  `approved_at` datetime DEFAULT NULL,
  `approved_by` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `token` (`approval_token`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

### Insert Default Admin User
```sql
-- Password: AdminTBS@123#$
INSERT INTO `users` (`username`, `firstName`, `lastName`, `email`, `phone`, `userType`, `password`) 
VALUES ('admin', 'Admin', 'User', 'admin@kathi.com', '9876543210', '1', '$2y$10$RhReit4tS4ghp5ZlkyHfS.K1Nl//nlXZ8lvglm0oCMNMomALHJ4ti');
```

---

## 👑 Admin Access

| Detail | Information |
|--------|-------------|
| **Admin Panel URL** | `http://localhost/table-booking-system/admin/` |
| **Username** | `admin` |
| **Password** | `AdminTBS@123#$` |
| **User Type** | Administrator |

### Default Admin Dashboard
After login, you'll have access to:
- **Dashboard** - Overview with security monitoring
- **Users** - Manage system users
- **Bookings** - View and manage all bookings
- **Manage Table** - Add/edit tables and generate QR codes
- **Messages** - View contact form submissions
- **Profile Settings** - Update profile and change password

---

## 📁 File Structure

```
table-booking-system/
├── index.php                    # Customer homepage
├── header.php                    # Customer header with booking form
├── footer.php                    # Customer footer
├── connection.php                # Database connection (root)
├── about.php                      # About page
├── contact.php                    # Contact page
├── status-details.php             # Booking status checker
├── lib/                           # Third-party libraries
├── css/                           # Stylesheets
├── js/                            # JavaScript files
├── img/                           # Images
│
├── admin/                         # Admin panel
│   ├── index.php                   # Admin dashboard
│   ├── header.php                   # Admin header
│   ├── footer.php                   # Admin footer
│   ├── login.php                    # Admin login
│   ├── booking.php                  # Manage bookings
│   ├── manage-table.php             # Table management
│   ├── user.php                      # User management
│   ├── contact.php                   # View messages
│   ├── profile_settings.php          # Profile settings
│   ├── test_session.php              # Session security test
│   ├── test_tampering.php            # Tamper detection test
│   ├── test_audit.php                # Audit trail test
│   ├── test_approval.php             # Approval system test
│   ├── tampering_logs.php            # View tampering logs
│   ├── mark_alert_read.php            # Mark alerts as read
│   ├── scan_database.php              # Manual database scan
│   ├── ajax_security_logs.php         # AJAX for logs modal
│   ├── ajax_security_alerts.php       # AJAX for alerts modal
│   │
│   ├── partials/                     # Include files
│   │   ├── connection.php              # Admin DB connection
│   │   ├── _handleLogin.php             # Login handler
│   │   ├── _logout.php                   # Logout handler
│   │   ├── _bookingManage.php            # Booking actions
│   │   ├── _bookingStatusModal.php       # Booking status modal
│   │   └── _tableManage.php              # Table management actions
│   │
│   └── includes/                      # Security classes
│       ├── behavior_analysis.php        # Feature 1
│       ├── database_security.php         # Feature 2
│       ├── session_security.php          # Feature 3
│       ├── audit_trail.php               # Feature 4
│       └── action_approval.php           # Feature 5
│
└── logs/                            # Security logs
    ├── tampering.log
    └── session.log
```

---

## 🧪 Testing the Security Features

### Test Simulators
Each security feature has its own test page:

| Feature | Test Page | URL |
|---------|-----------|-----|
| Admin Behavior Analysis | Built into dashboard | `admin/index.php` |
| Session Fingerprinting | `test_session.php` | `http://localhost/table-booking-system/admin/test_session.php` |
| Database Tamper Detection | `test_tampering.php` | `http://localhost/table-booking-system/admin/test_tampering.php` |
| Audit Trail | `test_audit.php` | `http://localhost/table-booking-system/admin/test_audit.php` |
| Action Approval | `test_approval.php` | `http://localhost/table-booking-system/admin/test_approval.php` |

### Quick Test Guide

#### 1. **Test Session Fingerprinting**
```bash
1. Login in Chrome
2. Open Firefox/Edge and try to login
3. Go back to Chrome → See both sessions in table
4. Click "Terminate Other Sessions"
5. Firefox gets logged out automatically
```

#### 2. **Test Tamper Detection**
```bash
1. Go to test_tampering.php
2. Click "Create Test Booking"
3. Click "Tamper Booking"
4. Click "Scan Now" → Red alert appears
5. Check dashboard → Tampering detected
```

#### 3. **Test Audit Trail**
```bash
1. Go to test_audit.php
2. Click INSERT, UPDATE, DELETE buttons
3. Watch logs appear in real-time
4. Click "Mass Delete" → Triggers anomaly alert
```

#### 4. **Test Action Approval**
```bash
1. Go to test_approval.php
2. Create approval request → Get 6-digit code
3. Enter token and code in verify form
4. Request gets approved
5. Check pending approvals table
```

---

## 🔧 Troubleshooting

### Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| **Database connection error** | Check credentials in `connection.php` and `admin/partials/connection.php` |
| **Admin login fails** | Verify password hash or reset using: `password_hash('AdminTBS@123#$', PASSWORD_DEFAULT)` |
| **Session errors** | Ensure `session_start()` is at the very top of all files |
| **Tamper detection false positives** | Run integrity check and update hashes |
| **Blank page** | Enable error reporting: `error_reporting(E_ALL); ini_set('display_errors', 1);` |
| **Modal not opening** | Check if jQuery and Bootstrap JS are loaded correctly |

### Quick Fixes
```bash
# Clear session logs
TRUNCATE TABLE admin_sessions;
TRUNCATE TABLE session_hijack_logs;

# Reset security alerts
UPDATE security_alerts SET is_read = 1;

# Clear tampering logs
DELETE FROM data_tampering_log WHERE detected_at < DATE_SUB(NOW(), INTERVAL 24 HOUR);
```

---

## 📊 System URLs Quick Reference

| Page | URL | Access |
|------|-----|--------|
| Customer Home | `http://localhost/table-booking-system/` | Public |
| About | `http://localhost/table-booking-system/about.php` | Public |
| Contact | `http://localhost/table-booking-system/contact.php` | Public |
| Status Check | `http://localhost/table-booking-system/status-details.php` | Public |
| Admin Login | `http://localhost/table-booking-system/admin/login.php` | Public |
| Admin Dashboard | `http://localhost/table-booking-system/admin/index.php` | Admin Only |
| Manage Bookings | `http://localhost/table-booking-system/admin/booking.php` | Admin Only |
| Manage Tables | `http://localhost/table-booking-system/admin/manage-table.php` | Admin Only |
| Profile Settings | `http://localhost/table-booking-system/admin/profile_settings.php` | Admin Only |

---

## 📝 License

Copyright © 2026 Kathi Restaurant. All rights reserved.

This project is proprietary and confidential. Unauthorized copying, distribution, or use is strictly prohibited.

---

## 👨‍💻 Support

For issues, questions, or feature requests:
- Create an issue on GitHub
- Contact the system administrator
- Check the logs folder for detailed error information

---

## ✅ Quick Start Summary

```bash
1. Install XAMPP
2. Start Apache & MySQL
3. Clone to C:\xampp\htdocs\table-booking-system\
4. Import database.sql to phpMyAdmin
5. Update connection.php if needed
6. Access customer site: http://localhost/table-booking-system/
7. Login to admin: http://localhost/table-booking-system/admin/
   - Username: admin
   - Password: AdminTBS@123#$
8. Explore security features via test pages
```

---

*Last Updated: March 2026*
*Version: 2.0*