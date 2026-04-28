# NewTrip Admin Dashboard

A modern, full-featured admin panel for the NewTrip travel management system built with Next.js 16+, TypeScript, Tailwind CSS, and MySQL.

## Features

### Core Modules
- 🏨 **Hotels Management** - Create, update, and manage hotel listings
- 📦 **Packages Management** - Travel packages with pricing and details
- 🗺️ **Destinations** - Geographic locations and travel destinations
- 🎉 **Events** - Special events and activities at destinations
- 👥 **Users Management** - Customer accounts and admin users
- 📅 **Bookings** - Track and manage all travel bookings
- 📄 **Invoices** - Billing and payment management
- ⚙️ **Settings** - System configuration and preferences

### Technology Stack
- **Frontend**: Next.js 16+ (App Router)
- **UI Components**: Shadcn/ui with Tailwind CSS
- **Database**: MySQL with mysql2 driver
- **Language**: TypeScript
- **Icons**: Lucide React

## Installation

### Prerequisites
- Node.js 18+ 
- MySQL 5.7+
- npm or yarn

### Setup Steps

1. **Install Dependencies**
```bash
npm install
```

2. **Environment Configuration**
Create a `.env.local` file in the project root:

```env
# Database Configuration
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=newtrip_db
DB_PORT=3306

# Application
NEXT_PUBLIC_API_URL=http://localhost:3000
NODE_ENV=development
```

3. **Database Setup**

Run the SQL script to create the database and tables:

```sql
CREATE DATABASE IF NOT EXISTS newtrip_db;

USE newtrip_db;

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
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

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
  FOREIGN KEY (destination_id) REFERENCES destinations(id)
);

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
  FOREIGN KEY (destination_id) REFERENCES destinations(id)
);

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
  FOREIGN KEY (user_id) REFERENCES users(id),
  FOREIGN KEY (package_id) REFERENCES packages(id),
  FOREIGN KEY (hotel_id) REFERENCES hotels(id),
  FOREIGN KEY (event_id) REFERENCES events(id)
);

CREATE TABLE invoices (
  id INT PRIMARY KEY AUTO_INCREMENT,
  booking_id INT,
  invoice_number VARCHAR(50) UNIQUE,
  amount DECIMAL(10,2),
  tax DECIMAL(10,2),
  total DECIMAL(10,2),
  status ENUM('pending', 'paid', 'overdue') DEFAULT 'pending',
  issue_date DATE,
  due_date DATE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (booking_id) REFERENCES bookings(id)
);

CREATE INDEX idx_user_email ON users(email);
CREATE INDEX idx_booking_user ON bookings(user_id);
CREATE INDEX idx_booking_package ON bookings(package_id);
CREATE INDEX idx_package_destination ON packages(destination_id);
```

4. **Start Development Server**
```bash
npm run dev
```

Visit `http://localhost:3000/dashboard` to access the admin panel.

## Project Structure

```
src/
├── app/
│   ├── (admin)/              # Admin routes with layout
│   │   ├── dashboard/
│   │   ├── hotels/
│   │   ├── packages/
│   │   ├── destinations/
│   │   ├── events/
│   │   ├── users/
│   │   ├── bookings/
│   │   ├── invoices/
│   │   ├── settings/
│   │   └── layout.tsx
│   └── api/                   # API routes for database operations
│       ├── hotels/
│       ├── bookings/
│       └── users/
├── components/
│   └── admin/                # Reusable admin components
│       ├── sidebar.tsx
│       ├── topbar.tsx
│       ├── dashboard-stats.tsx
│       └── recent-bookings.tsx
├── lib/
│   └── db.ts                 # Database connection and utilities
└── types/
    └── index.ts              # TypeScript type definitions
```

## API Endpoints

### Hotels
- `GET /api/hotels` - List all hotels
- `POST /api/hotels` - Create new hotel
- `GET /api/hotels/[id]` - Get specific hotel
- `PUT /api/hotels/[id]` - Update hotel
- `DELETE /api/hotels/[id]` - Delete hotel

### Bookings
- `GET /api/bookings` - List all bookings
- `POST /api/bookings` - Create new booking
- `GET /api/bookings/[id]` - Get specific booking
- `PUT /api/bookings/[id]` - Update booking status
- `DELETE /api/bookings/[id]` - Cancel booking

### Users
- `GET /api/users` - List all users
- `POST /api/users` - Create new user
- `GET /api/users/[id]` - Get specific user
- `PUT /api/users/[id]` - Update user
- `DELETE /api/users/[id]` - Delete user

## Adding More API Routes

Create new files in `src/app/api/` following the pattern:

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { query } from '@/lib/db';

export async function GET() {
  try {
    const data = await query('SELECT * FROM your_table');
    return NextResponse.json(data);
  } catch (error) {
    return NextResponse.json({ error: 'Failed to fetch data' }, { status: 500 });
  }
}
```

## Building for Production

```bash
npm run build
npm start
```

## Environment Variables Reference

| Variable | Description | Default |
|----------|-------------|---------|
| DB_HOST | MySQL host | localhost |
| DB_USER | MySQL username | root |
| DB_PASSWORD | MySQL password | (empty) |
| DB_NAME | Database name | newtrip_db |
| DB_PORT | MySQL port | 3306 |
| NEXT_PUBLIC_API_URL | API base URL | http://localhost:3000 |

## Next Steps

1. **Authentication** - Implement JWT or session-based authentication
2. **Dashboard Charts** - Add analytics and reporting
3. **Image Upload** - Integrate cloud storage (AWS S3, Cloudinary)
4. **Email Notifications** - Send booking confirmations and reminders
5. **Search & Filters** - Advanced filtering for all modules
6. **Export to PDF** - Generate invoices and reports as PDF
7. **Mobile Responsive** - Ensure full mobile compatibility

## Support

For issues or questions, refer to the documentation or check the GitHub repository.

## License

NewTrip Admin Dashboard © 2024. All rights reserved.
