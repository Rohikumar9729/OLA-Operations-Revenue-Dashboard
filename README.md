# OLA Data Analyst Project 🚖📊

An end-to-end data analysis project analyzing 100,000 synthetic ride bookings for Bengaluru city. The project covers SQL queries to solve key business questions and Power BI visual dashboard structures to track ride volumes, revenue, cancellations, and ratings.

---

## 📌 Table of Contents
- [Project Overview](#-project-overview)
- [Dataset Architecture](#-dataset-architecture)
- [SQL Analysis & Views](#-sql-analysis--views)
- [Power BI Dashboard Design](#-power-bi-dashboard-design)
- [Key Insights & Metrics](#-key-insights--metrics)
- [How to Run](#-how-to-run)

---

## 📖 Project Overview
This project simulates real-world cab aggregator data (OLA) to extract meaningful business insights. 

* **City:** Bengaluru
* **Volume:** 100,000 records across 1 month
* **Target Success Rate:** ~62%
* **Max Customer Cancellation Rate:** < 7%
* **Max Driver Cancellation Rate:** < 18%
* **Incomplete Rides:** < 6%

---

## 🗂️ Dataset Architecture
The dataset consists of 19 attributes:

| Column Name | Description |
| :--- | :--- |
| `Date` | Booking date |
| `Time` | Booking time |
| `Booking_ID` | Unique identifier (CNR + 10 digits)[cite: 1] |
| `Booking_Status` | Status (Success, Cancelled by Customer, Cancelled by Driver, etc.)[cite: 1] |
| `Customer_ID` | Unique customer identifier[cite: 1] |
| `Vehicle_Type` | Auto, Prime Plus, Prime Sedan, Mini, Bike, eBike, Prime SUV[cite: 1] |
| `Pickup_Location` | Pickup area (50 Bangalore locations)[cite: 1] |
| `Drop_Location` | Drop area[cite: 1] |
| `V_TAT` | Arrival time to vehicle[cite: 1] |
| `C_TAT` | Arrival time to customer[cite: 1] |
| `cancelled_Rides_by_Customer` | Customer cancellation indicator/count[cite: 1] |
| `cancelled_Rides_by_Driver` | Driver cancellation reason/indicator[cite: 1] |
| `Incomplete_Rides` | Incomplete ride flag (`Yes`/`No`)[cite: 1] |
| `Incomplete_Rides_Reason` | Customer Demand, Vehicle Breakdown, Other Issue[cite: 1] |
| `Booking_Value` | Fare charged for the trip[cite: 1] |
| `Payment_Method` | UPI, Cash, Credit Card, etc.[cite: 1] |
| `Ride_Distance` | Total distance traveled[cite: 1] |
| `Driver_Ratings` | Driver rating score[cite: 1] |
| `Customer_Rating` | Customer rating score[cite: 1] |

---

## 🛠️ SQL Analysis & Views

```sql
CREATE DATABASE Ola;
USE Ola;

-- 1. Retrieve all successful bookings
CREATE VIEW Successful_Bookings AS
SELECT * FROM bookings
WHERE Booking_Status = 'Success';

-- 2. Find the average ride distance for each vehicle type
CREATE VIEW ride_distance_for_each_vehicle AS
SELECT Vehicle_Type, AVG(Ride_Distance) AS avg_distance 
FROM bookings
GROUP BY Vehicle_Type;

-- 3. Get total cancelled rides by customers
CREATE VIEW cancelled_rides_by_customers AS
SELECT COUNT(*) AS total_customer_cancellations 
FROM bookings
WHERE Booking_Status = 'cancelled by Customer';

-- 4. Top 5 customers who booked the highest number of rides
CREATE VIEW Top_5_Customers AS
SELECT Customer_ID, COUNT(Booking_ID) AS total_rides
FROM bookings
GROUP BY Customer_ID
ORDER BY total_rides DESC 
LIMIT 5;

-- 5. Rides cancelled by drivers due to personal and car-related issues
CREATE VIEW Rides_cancelled_by_Drivers_P_C_Issues AS
SELECT COUNT(*) AS driver_pc_cancellations 
FROM bookings
WHERE cancelled_Rides_by_Driver = 'Personal & Car related issue';

-- 6. Max and Min driver ratings for Prime Sedan
CREATE VIEW Max_Min_Driver_Rating AS
SELECT MAX(Driver_Ratings) AS max_rating, MIN(Driver_Ratings) AS min_rating
FROM bookings 
WHERE Vehicle_Type = 'Prime Sedan';

-- 7. Retrieve all rides where payment was made using UPI
CREATE VIEW UPI_Payment AS
SELECT * FROM bookings
WHERE Payment_Method = 'UPI';

-- 8. Average customer rating per vehicle type
CREATE VIEW AVG_Cust_Rating AS
SELECT Vehicle_Type, AVG(Customer_Rating) AS avg_customer_rating
FROM bookings
GROUP BY Vehicle_Type;

-- 9. Total booking value of rides completed successfully
CREATE VIEW total_successful_ride_value AS
SELECT SUM(Booking_Value) AS total_successful_value
FROM bookings
WHERE Booking_Status = 'Success';

-- 10. List all incomplete rides along with reasons
CREATE VIEW Incomplete_Rides_Reason AS
SELECT Booking_ID, Incomplete_Rides_Reason
FROM bookings
WHERE Incomplete_Rides = 'Yes';
