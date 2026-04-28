# Parking Space Sales Management System (SmartPark)

**Developer:** Kagiraneza Generas   

---

## Project Overview

SmartPark is a web-based parking management system that digitizes the parking space sales process. It replaces the manual, paper-based system with an automated solution that:

- Records car entries and exits digitally
- Automatically calculates parking fees
- Updates slot availability in real-time
- Generates bills and daily reports
- Manages user authentication securely

---

## Folder Structure

```
KAGIRANEZA_GENERAS_NATIONAL_PRACTICAL+EXAM_2026/
├── BACKEND-PROJECT/          # Node.js Express Backend
│   ├── config/
│   │   └── db.js            # Database connection
│   ├── routes/
│   │   ├── auth.js          # Authentication routes
│   │   ├── cars.js          # Car management
│   │   ├── slots.js         # Parking slot management
│   │   ├── records.js       # Parking records (Full CRUD)
│   │   ├── payments.js      # Payment processing
│   │   └── reports.js       # Report generation
│   ├── .env                 # Environment configuration
│   ├── package.json         # Dependencies
│   └── server.js            # Main server file
│
├── FRONTEND-PROJECT/         # React Frontend
│   ├── src/
│   │   ├── components/
│   │   │   ├── Login.jsx
│   │   │   ├── Layout.jsx
│   │   │   ├── Car.jsx
│   │   │   ├── ParkingSlot.jsx
│   │   │   ├── ParkingRecord.jsx
│   │   │   ├── Payment.jsx
│   │   │   └── Reports.jsx
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   ├── index.css        # Tailwind CSS v4
│   │   └── axiosConfig.js   # API configuration
│   ├── package.json
│   ├── vite.config.js
│   └── index.html
│
└── database.sql              # MySQL Database Schema
```

---

## Database Schema (ERD)

### Entities and Relationships:

```
┌─────────────────┐       ┌──────────────────┐       ┌─────────────────┐
│  ParkingSlots   │       │  ParkingRecords  │       │  Payments       │
├─────────────────┤       ├──────────────────┤       ├─────────────────┤
│ PK slotId       │<──────┤ PK recordId      │<──────┤ PK paymentId    │
│    slotNumber   │   1:N │ FK carPlateNumber│  1:1  │    FK recordId  │
│    slotStatus   │       │ FK slotId        │       │    amountPaid   │
└─────────────────┘       │    entryTime     │       │    paymentDate  │
                        │    exitTime      │       └─────────────────┘
┌─────────────────┐      │    duration      │
│      Cars       │      │    totalAmount   │
├─────────────────┤      │    status        │
│ PK carPlateNumber│<────┘    createdAt    │
│    driverName   │                       └──────┐
│    phoneNumber  │                             │
└─────────────────┘                             │
                                                │
┌─────────────────┐                             │
│      Users      │                             │
├─────────────────┤      (Authentication only)  │
│ PK userId       │◄─────────────────────────────┘
│    username     │
│    password     │
└─────────────────┘
```

### Cardinality:
- **ParkingSlots 1:N ParkingRecords** - One slot can have many records over time
- **Cars 1:N ParkingRecords** - One car can have many visits
- **ParkingRecords 1:1 Payments** - One record has one payment
- **Users** - Standalone for authentication only

---

## Installation Instructions

### Prerequisites
- Node.js (v18 or higher)
- MySQL Server
- npm (comes with Node.js)

### Step 1: Database Setup

1. Open MySQL or MySQL Workbench
2. Run the database.sql file:
   ```sql
   SOURCE database.sql;
   ```
3. Or copy-paste the SQL commands into MySQL

### Step 2: Backend Setup

```bash
cd BACKEND-PROJECT

# Install dependencies
npm install

# Configure environment
# Edit .env file with your MySQL password:
DB_PASSWORD=your_mysql_password

# Start server
npm start
# OR for development with auto-reload:
npm run dev
```

Backend runs on: http://localhost:5000

### Step 3: Frontend Setup

```bash
cd FRONTEND-PROJECT

# Install dependencies
npm install

# Start development server
npm run dev
```

Frontend runs on: http://localhost:5173

---

## How to Use

### 1. Login
- Open http://localhost:5173
- **Username:** admin
- **Password:** admin123

### 2. Navigation Menu
- **Car** - Register new cars and drivers
- **Parking Slot** - Manage parking spaces
- **Parking Record** - Record entries, exits, and generate bills (Full CRUD)
- **Payment** - Record payments for completed parking
- **Reports** - Generate daily payment reports
- **Logout** - End session

### 3. Typical Workflow

**Step 1:** Register a Car
1. Go to "Car" page
2. Enter car plate number, driver name, phone
3. Click "Register Car"

**Step 2:** Add Parking Slots
1. Go to "Parking Slot" page
2. Enter slot number (e.g., A1, B5)
3. Click "Create Slot"

**Step 3:** Record Car Entry
1. Go to "Parking Record" page
2. Select car from dropdown
3. Select available slot
4. Set entry time
5. Click "Record Entry"

**Step 4:** Record Car Exit & Generate Bill
1. Find the active record in the table
2. Click the exit icon (logout icon)
3. Confirm exit time
4. Bill is automatically generated showing:
   - Plate number
   - Entry/Exit times
   - Duration
   - Amount (500 RWF per hour, rounded up)

**Step 5:** Record Payment
1. Go to "Payment" page
2. Select completed parking record
3. Enter amount paid
4. Click "Record Payment"

**Step 6:** Generate Reports
1. Go to "Reports" page
2. Select date
3. Click "Generate Report"
4. View daily summary and detailed transactions

---

## Technical Details

### Parking Fee Calculation
- **Rate:** 500 RWF per hour
- **Calculation:** Hours parked × 500 RWF
- **Minimum charge:** 1 hour (any duration under 1 hour = 1 hour charge)
- **Example:** Parked for 2.5 hours = 3 hours × 500 = 1500 RWF

### Authentication
- Session-based authentication using express-session
- Passwords encrypted with bcryptjs
- Session expires after 1 hour of inactivity

### API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| /api/auth/login | POST | User login |
| /api/auth/logout | POST | User logout |
| /api/cars | POST | Create car |
| /api/cars | GET | List all cars |
| /api/slots | POST | Create slot |
| /api/slots | GET | List all slots |
| /api/records | POST | Create record |
| /api/records | GET | List all records |
| /api/records/:id | PUT | Update record |
| /api/records/:id | DELETE | Delete record |
| /api/records/:id/exit | PUT | Record exit |
| /api/payments | POST | Create payment |
| /api/reports/daily | GET | Daily report |
| /api/reports/bill/:id | GET | Generate bill |

---

## Features Summary

### Backend (Node.js + Express + MySQL)
- Session-based authentication
- CRUD operations with proper error handling
- Automatic fee calculation
- Database relationships with foreign keys
- Input validation and sanitization

### Frontend (React + Tailwind CSS v4)
- Responsive design (works on mobile and desktop)
- Protected routes (requires login)
- Real-time form validation
- Interactive bill generation
- Daily reports with print support

---

## Technologies Used

| Component | Technology |
|-----------|------------|
| Backend Runtime | Node.js |
| Backend Framework | Express.js |
| Database | MySQL |
| Frontend Framework | React 18 |
| Build Tool | Vite |
| Styling | Tailwind CSS v4 |
| HTTP Client | Axios |
| Icons | Lucide React |
| Authentication | bcryptjs + express-session |

---

## Troubleshooting

### Backend won't start
- Check MySQL is running
- Verify .env file has correct password
- Check port 5000 is not in use

### Frontend can't connect
- Ensure backend is running on port 5000
- Check CORS settings in server.js
- Verify Vite proxy configuration

### Database errors
- Run database.sql to create tables
- Check MySQL user has proper permissions
- Verify database name is smartparkwindsurf

---

## Notes for National Exam

### Key Points to Remember:

1. **ERD Design:**
   - Always identify PRIMARY KEYS (unique identifier for each entity)
   - Use FOREIGN KEYS to create relationships
   - Show cardinality (1:1, 1:N, M:N)
   - Draw on paper with proper symbols

2. **Database Creation:**
   - Create database first: `CREATE DATABASE smartparkwindsurf;`
   - Use `USE smartparkwindsurf;` to select database
   - Define tables with proper data types
   - Set up foreign key constraints

3. **Backend Development:**
   - Initialize with `npm init -y`
   - Install required packages
   - Create separate route files for each entity
   - Use async/await for database operations
   - Handle errors properly

4. **Frontend Development:**
   - Use Vite for faster development
   - Install Tailwind CSS v4
   - Create reusable components
   - Use React Router for navigation
   - Implement form validation

5. **Integration:**
   - Configure Axios with baseURL
   - Enable CORS in backend
   - Use credentials for session management
   - Test API endpoints before UI integration

---

## Contact

**Name:** Kagiraneza Generas  
**Exam:** National Practical Examination 2026

---

*This project was developed for the National Practical Examination 2026.*
