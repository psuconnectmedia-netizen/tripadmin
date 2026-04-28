# Database Setup Guide for NewTrip Admin

This guide helps you set up the MySQL database and connect it to the Next.js admin dashboard.

## Prerequisites

1. **MySQL Server** - Install MySQL Community Server
   - [Download MySQL](https://dev.mysql.com/downloads/mysql/)
   - During installation, set root password (remember it!)

2. **MySQL Workbench** (Optional but recommended)
   - [Download MySQL Workbench](https://www.mysql.com/products/workbench/)
   - Visual tool for managing databases

3. **PHPMyAdmin Access** (if using XAMPP/WAMP)
   - Usually available at `http://localhost/phpmyadmin`

## Quick Setup

### Option 1: Using MySQL Command Line

1. Open Command Prompt/Terminal
2. Connect to MySQL:
```bash
mysql -u root -p
```
Enter your root password when prompted

3. Copy and paste the SQL from [Database Schema](#database-schema) section
4. Press Enter to execute

### Option 2: Using MySQL Workbench

1. Open MySQL Workbench
2. Click "+ Create a new SQL tab for executing queries"
3. Paste the SQL code
4. Click the lightning bolt icon to execute

### Option 3: Using PHPMyAdmin

1. Go to `http://localhost/phpmyadmin`
2. Click on "SQL" tab
3. Paste the SQL code
4. Click "Go"

## Database Schema

```sql
CREATE DATABASE IF NOT EXISTS newtrip_db;
USE newtrip_db;

-- Users Table
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  email VARCHAR(255) UNIQUE NOT NULL,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  phone VARCHAR(20),
  password VARCHAR(255),
  role ENUM('admin', 'user') DEFAULT 'user',
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_email (email)
);

-- Hotels Table
CREATE TABLE hotels (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  city VARCHAR(100),
  country VARCHAR(100),
  address TEXT,
  rating DECIMAL(2,1),
  price_per_night DECIMAL(10,2),
  description TEXT,
  image_url VARCHAR(500),
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Destinations Table
CREATE TABLE destinations (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  country VARCHAR(100),
  description TEXT,
  image_url VARCHAR(500),
  latitude DECIMAL(10,8),
  longitude DECIMAL(11,8),
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Packages Table
CREATE TABLE packages (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  destination_id INT,
  duration_days INT,
  price DECIMAL(10,2),
  description TEXT,
  image_url VARCHAR(500),
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (destination_id) REFERENCES destinations(id) ON DELETE SET NULL
);

-- Events Table
CREATE TABLE events (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  destination_id INT,
  event_date DATE,
  event_time TIME,
  description TEXT,
  capacity INT,
  price DECIMAL(10,2),
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (destination_id) REFERENCES destinations(id) ON DELETE SET NULL
);

-- Bookings Table
CREATE TABLE bookings (
  id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT,
  package_id INT,
  hotel_id INT,
  event_id INT,
  booking_date DATE,
  check_in_date DATE,
  check_out_date DATE,
  number_of_guests INT,
  total_price DECIMAL(10,2),
  status ENUM('pending', 'confirmed', 'cancelled') DEFAULT 'pending',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
  FOREIGN KEY (package_id) REFERENCES packages(id) ON DELETE SET NULL,
  FOREIGN KEY (hotel_id) REFERENCES hotels(id) ON DELETE SET NULL,
  FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE SET NULL
);

-- Invoices Table
CREATE TABLE invoices (
  id INT PRIMARY KEY AUTO_INCREMENT,
  booking_id INT,
  invoice_number VARCHAR(50) UNIQUE NOT NULL,
  amount DECIMAL(10,2),
  tax DECIMAL(10,2),
  total DECIMAL(10,2),
  status ENUM('pending', 'paid', 'overdue') DEFAULT 'pending',
  issue_date DATE,
  due_date DATE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (booking_id) REFERENCES bookings(id) ON DELETE CASCADE
);

-- Create Indexes for better performance
CREATE INDEX idx_booking_user ON bookings(user_id);
CREATE INDEX idx_booking_package ON bookings(package_id);
CREATE INDEX idx_booking_status ON bookings(status);
CREATE INDEX idx_package_destination ON packages(destination_id);
CREATE INDEX idx_event_destination ON events(destination_id);
```

## Environment Configuration

Create `.env.local` file in your project root:

```env
# MySQL Database Configuration
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_root_password_here
DB_NAME=newtrip_db
DB_PORT=3306

# Application URLs
NEXT_PUBLIC_API_URL=http://localhost:3000
NODE_ENV=development
```

Replace `your_root_password_here` with your actual MySQL root password.

## Test Database Connection

After setting up, test the connection:

```bash
npm run dev
```

If you can access `http://localhost:3000/dashboard` without errors, your database is properly configured.

## Sample Data

Insert sample data for testing:

```sql
USE newtrip_db;

-- Insert Admin User
INSERT INTO users (email, first_name, last_name, phone, password, role)
VALUES ('admin@newtrip.com', 'Admin', 'User', '+1234567890', 'hashed_password', 'admin');

-- Insert Sample Destinations
INSERT INTO destinations (name, country, description, latitude, longitude)
VALUES 
  ('Dubai', 'UAE', 'Modern city in the desert', 25.2048, 55.2708),
  ('Maldives', 'Maldives', 'Tropical paradise islands', 4.1694, 73.5093),
  ('Bangkok', 'Thailand', 'Capital of Thailand', 13.7563, 100.5018);

-- Insert Sample Hotels
INSERT INTO hotels (name, city, country, address, rating, price_per_night)
VALUES 
  ('Burj Al Arab', 'Dubai', 'UAE', 'Jumeirah Beach', 5.0, 350.00),
  ('Atlantis The Royal', 'Dubai', 'UAE', 'The Palm Jumeirah', 5.0, 420.00),
  ('Conrad Maldives', 'Maldives', 'Maldives', 'Rangali Island', 5.0, 500.00);

-- Insert Sample Packages
INSERT INTO packages (name, destination_id, duration_days, price)
VALUES 
  ('Dubai Experience', 1, 5, 1200.00),
  ('Maldives Paradise', 2, 7, 2500.00),
  ('Bangkok Adventure', 3, 4, 800.00);
```

## Troubleshooting

### Connection Error: "Can't connect to MySQL server"
- Ensure MySQL server is running
- Check DB_HOST and DB_PORT in `.env.local`
- Verify credentials are correct

### Port Already in Use
- MySQL default port: 3306
- Change port in `.env.local` if needed

### Permission Denied
- Ensure MySQL user has proper permissions
- Try using root user for testing

### Database Not Found
- Run the SQL schema creation script again
- Check database name in `.env.local` matches created database

## Backup Database

Backup your data regularly:

```bash
# Windows
mysqldump -u root -p newtrip_db > backup.sql

# Linux/Mac
mysqldump -u root -p newtrip_db > backup.sql
```

## Restore Database

```bash
mysql -u root -p newtrip_db < backup.sql
```

## Additional Resources

- [MySQL Documentation](https://dev.mysql.com/doc/)
- [MySQL Workbench Guide](https://dev.mysql.com/doc/workbench/en/)
- [Next.js Database Integration](https://nextjs.org/learn/dashboard-app/setting-up-your-database)

## Next Steps

1. Verify all tables are created in PHPMyAdmin
2. Insert sample data for testing
3. Run the development server
4. Start building API routes to interact with the database
5. Implement authentication for admin users
