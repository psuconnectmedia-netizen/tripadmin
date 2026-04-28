# NewTrip Admin Dashboard - Quick Start Guide

Get your admin panel up and running in 5 minutes!

## 🚀 Quick Start (5 minutes)

### Step 1: Database Setup (2 minutes)

1. Open MySQL Workbench or Command Line
2. Run this single command:

```sql
CREATE DATABASE IF NOT EXISTS newtrip_db;
USE newtrip_db;

CREATE TABLE users (id INT PRIMARY KEY AUTO_INCREMENT, email VARCHAR(255) UNIQUE NOT NULL, first_name VARCHAR(100), last_name VARCHAR(100), phone VARCHAR(20), password VARCHAR(255), role ENUM('admin', 'user') DEFAULT 'user', is_active BOOLEAN DEFAULT TRUE, created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, INDEX idx_email (email));
CREATE TABLE hotels (id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(255) NOT NULL, city VARCHAR(100), country VARCHAR(100), address TEXT, rating DECIMAL(2,1), price_per_night DECIMAL(10,2), description TEXT, image_url VARCHAR(500), is_active BOOLEAN DEFAULT TRUE, created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP);
CREATE TABLE destinations (id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(255) NOT NULL, country VARCHAR(100), description TEXT, image_url VARCHAR(500), latitude DECIMAL(10,8), longitude DECIMAL(11,8), is_active BOOLEAN DEFAULT TRUE, created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP);
CREATE TABLE packages (id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(255) NOT NULL, destination_id INT, duration_days INT, price DECIMAL(10,2), description TEXT, image_url VARCHAR(500), is_active BOOLEAN DEFAULT TRUE, created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, FOREIGN KEY (destination_id) REFERENCES destinations(id) ON DELETE SET NULL);
CREATE TABLE events (id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(255) NOT NULL, destination_id INT, event_date DATE, event_time TIME, description TEXT, capacity INT, price DECIMAL(10,2), is_active BOOLEAN DEFAULT TRUE, created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, FOREIGN KEY (destination_id) REFERENCES destinations(id) ON DELETE SET NULL);
CREATE TABLE bookings (id INT PRIMARY KEY AUTO_INCREMENT, user_id INT, package_id INT, hotel_id INT, event_id INT, booking_date DATE, check_in_date DATE, check_out_date DATE, number_of_guests INT, total_price DECIMAL(10,2), status ENUM('pending', 'confirmed', 'cancelled') DEFAULT 'pending', created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE, FOREIGN KEY (package_id) REFERENCES packages(id) ON DELETE SET NULL, FOREIGN KEY (hotel_id) REFERENCES hotels(id) ON DELETE SET NULL, FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE SET NULL);
CREATE TABLE invoices (id INT PRIMARY KEY AUTO_INCREMENT, booking_id INT, invoice_number VARCHAR(50) UNIQUE NOT NULL, amount DECIMAL(10,2), tax DECIMAL(10,2), total DECIMAL(10,2), status ENUM('pending', 'paid', 'overdue') DEFAULT 'pending', issue_date DATE, due_date DATE, created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, FOREIGN KEY (booking_id) REFERENCES bookings(id) ON DELETE CASCADE);
```

### Step 2: Environment Setup (1 minute)

Create `.env.local` in project root:

```env
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=newtrip_db
DB_PORT=3306
NEXT_PUBLIC_API_URL=http://localhost:3000
NODE_ENV=development
```

### Step 3: Start Development Server (1 minute)

```bash
npm run dev
```

### Step 4: Access Dashboard (1 minute)

Visit: **http://localhost:3000/dashboard**

## 📋 Features Overview

| Feature | Route | Description |
|---------|-------|-------------|
| Dashboard | `/dashboard` | Overview and statistics |
| Hotels | `/hotels` | Manage hotel listings |
| Packages | `/packages` | Travel packages |
| Destinations | `/destinations` | Geographic locations |
| Events | `/events` | Special events |
| Users | `/users` | Customer management |
| Bookings | `/bookings` | Booking management |
| Invoices | `/invoices` | Billing system |
| Settings | `/settings` | System configuration |

## 🔌 API Examples

### Get All Hotels
```bash
curl http://localhost:3000/api/hotels
```

### Create New Hotel
```bash
curl -X POST http://localhost:3000/api/hotels \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Burj Al Arab",
    "city": "Dubai",
    "country": "UAE",
    "price_per_night": 350,
    "rating": 5
  }'
```

### Get All Users
```bash
curl http://localhost:3000/api/users
```

### Create Booking
```bash
curl -X POST http://localhost:3000/api/bookings \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": 1,
    "package_id": 1,
    "booking_date": "2024-01-15",
    "check_in_date": "2024-02-15",
    "check_out_date": "2024-02-20",
    "number_of_guests": 2,
    "total_price": 2400
  }'
```

## 🗂️ Project Structure Quick Reference

```
newtrip/
├── src/
│   ├── app/
│   │   ├── (admin)/
│   │   │   ├── dashboard/page.tsx      ← Dashboard
│   │   │   ├── hotels/page.tsx         ← Hotels management
│   │   │   ├── packages/page.tsx       ← Packages management
│   │   │   ├── users/page.tsx          ← Users management
│   │   │   ├── bookings/page.tsx       ← Bookings management
│   │   │   ├── invoices/page.tsx       ← Invoices management
│   │   │   └── layout.tsx              ← Admin layout (sidebar, topbar)
│   │   ├── api/
│   │   │   ├── hotels/route.ts
│   │   │   ├── packages/route.ts
│   │   │   ├── bookings/route.ts
│   │   │   ├── users/route.ts
│   │   │   ├── invoices/route.ts
│   │   │   └── dashboard/route.ts
│   ├── components/admin/               ← Reusable components
│   │   ├── sidebar.tsx
│   │   ├── topbar.tsx
│   │   ├── dashboard-stats.tsx
│   │   └── recent-bookings.tsx
│   ├── lib/db.ts                      ← Database connection
│   └── types/index.ts                 ← TypeScript types
├── .env.local                         ← Environment variables (create this!)
├── ADMIN_SETUP.md                     ← Full documentation
├── DATABASE_SETUP.md                  ← Database guide
└── QUICKSTART.md                      ← This file
```

## 📚 File Descriptions

### Pages to Customize
- **`src/app/(admin)/dashboard/page.tsx`** - Main dashboard view
- **`src/app/(admin)/[section]/page.tsx`** - List pages for each module
- **`src/components/admin/`** - UI components

### Database Layer
- **`src/lib/db.ts`** - MySQL connection and query utilities
- **`src/app/api/[resource]/route.ts`** - API endpoints

### Type Definitions
- **`src/types/index.ts`** - All TypeScript interfaces

## 🔧 Common Tasks

### Add a New Module

1. Create page file:
```bash
mkdir -p src/app/\(admin\)/mymodule
touch src/app/\(admin\)/mymodule/page.tsx
```

2. Create API route:
```bash
mkdir -p src/app/api/mymodule
touch src/app/api/mymodule/route.ts
```

3. Update sidebar in `src/components/admin/sidebar.tsx`

### Modify Database Schema

Edit `DATABASE_SETUP.md` and re-run the SQL script, then update types in `src/types/index.ts`

### Connect to Existing MySQL Database

Just update `.env.local` with your database credentials and existing tables will work!

## 🐛 Troubleshooting

### Dashboard won't load
```bash
# Check if server is running
curl http://localhost:3000/dashboard

# Restart development server
npm run dev
```

### Database connection error
- Verify `.env.local` exists and has correct credentials
- Check MySQL server is running
- Test connection: `mysql -u root -p -h localhost`

### Port 3000 already in use
```bash
npm run dev -- -p 3001
```

### Module not appearing in sidebar
- Add menu item to `src/components/admin/sidebar.tsx`
- Restart development server

## 📖 Next Steps

1. **Add Sample Data** - Run SQL INSERT statements from `DATABASE_SETUP.md`
2. **Customize Styling** - Edit Tailwind classes in components
3. **Add Authentication** - Implement login functionality
4. **Connect Frontend** - Use API endpoints in your public site
5. **Deploy** - Deploy to Vercel, Heroku, or your own server

## 🚀 Production Build

```bash
npm run build
npm start
```

Visit `http://localhost:3000` (production mode)

## 📞 Support

- Check `ADMIN_SETUP.md` for detailed documentation
- Check `DATABASE_SETUP.md` for database help
- Review component files for code examples

## ✅ Checklist

- [ ] MySQL database created
- [ ] `.env.local` configured with credentials
- [ ] `npm install` completed
- [ ] `npm run dev` runs without errors
- [ ] Dashboard loads at `http://localhost:3000/dashboard`
- [ ] All menu items appear in sidebar
- [ ] API endpoints respond to requests

**You're all set! Happy coding! 🎉**
