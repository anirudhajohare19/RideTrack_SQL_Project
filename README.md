# 🚗 RideTrack SQL Project

## ⚪️ Introduction
RideTrack is a SQL-based ride-booking system that manages riders, drivers, vehicles, rides, payments, and ratings. It simulates a real-world application similar to Uber or Lyft, providing essential analytics and insights for operational efficiency.

## ⚪️ Objective of the Project
- Efficiently store and retrieve ride-booking data.
- Ensure data consistency using foreign keys, constraints, and normalization.
- Analyze ride trends, payment methods, and driver performance.
- Provide business insights into ride demand, revenue, and customer feedback.
- Support decision-making through data-driven reports and queries.

## ⚪️ Data Insights
- **User Distribution:** Analyze the total number of riders and drivers.
- **Most Popular Pickup Locations:** Identify high-demand areas.
- **Revenue Trends:** Calculate total revenue generated from completed rides.
- **Driver Performance:** Find the highest-rated drivers and their ride count.
- **Pending Payments:** Identify unpaid transactions and analyze failed payments.

---

## Schema Design
### 1. Users Table
```sql
CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(15) UNIQUE NOT NULL,
    user_type ENUM('rider', 'driver') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
### 2. Vehicles Table
```sql
CREATE TABLE Vehicles (
    vehicle_id INT PRIMARY KEY AUTO_INCREMENT,
    driver_id INT,
    vehicle_type ENUM('sedan', 'suv', 'hatchback', 'bike') NOT NULL,
    vehicle_number VARCHAR(20) UNIQUE NOT NULL,
    model VARCHAR(50) NOT NULL
);
```
### 3. Rides Table
```sql
CREATE TABLE Rides (
    ride_id INT PRIMARY KEY AUTO_INCREMENT,
    rider_id INT NOT NULL,
    driver_id INT NOT NULL,
    vehicle_id INT NOT NULL,
    pickup_location VARCHAR(255) NOT NULL,
    dropoff_location VARCHAR(255) NOT NULL,
    fare DECIMAL(10,2) NOT NULL,
    distance_km DECIMAL(5,2) NOT NULL,
    status ENUM('requested', 'ongoing', 'completed', 'cancelled') NOT NULL,
    pickup_time DATETIME NOT NULL,
    dropoff_time DATETIME NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP NULL
);
```
### 4. Payments Table
```sql
CREATE TABLE Payments (
    payment_id INT PRIMARY KEY AUTO_INCREMENT,
    ride_id INT UNIQUE NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    payment_mode ENUM('cash', 'credit_card', 'debit_card', 'wallet') NOT NULL,
    status ENUM('pending', 'completed', 'failed') NOT NULL DEFAULT 'pending',
    transaction_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
### 5. Ratings Table
```sql
CREATE TABLE Ratings (
    rating_id INT PRIMARY KEY AUTO_INCREMENT,
    ride_id INT UNIQUE NOT NULL,
    rider_rating DECIMAL(2,1) CHECK (rider_rating BETWEEN 1 AND 5),
    driver_rating DECIMAL(2,1) CHECK (driver_rating BETWEEN 1 AND 5),
    rider_feedback TEXT,
    driver_feedback TEXT
);
```
---

## Queries For Analysis
### 1. Count the Total Number of Riders and Drivers
```sql
SELECT user_type, COUNT(*) AS total_users FROM Users GROUP BY user_type;
```
### 2. List of All Drivers with Their Vehicles
```sql
SELECT u.user_id, u.name, u.phone, v.vehicle_type, v.vehicle_number, v.model 
FROM Users u
LEFT JOIN Vehicles v ON u.user_id = v.driver_id
WHERE u.user_type = 'driver';
```
### 3. Find Riders Who Have Never Taken a Ride
```sql
SELECT u.user_id, u.name, u.email, u.phone 
FROM Users u
LEFT JOIN Rides r ON u.user_id = r.rider_id
WHERE u.user_type = 'rider' AND r.ride_id IS NULL;
```
### 4. Total Rides Completed vs. Cancelled
```sql
SELECT status, COUNT(*) AS total_rides FROM Rides GROUP BY status;
```
### 5. Average Fare and Distance of Completed Rides
```sql
SELECT ROUND(AVG(fare), 2) AS avg_fare, ROUND(AVG(distance_km), 2) AS avg_distance 
FROM Rides WHERE status = 'completed';
```
### 6. Driver Who Completed the Most Rides
```sql
SELECT driver_id, COUNT(*) AS total_rides 
FROM Rides 
WHERE status = 'completed' 
GROUP BY driver_id 
ORDER BY total_rides DESC 
LIMIT 1;
```
### 7. Find the Most Popular Pickup Location
```sql
SELECT pickup_location, COUNT(*) AS total_rides 
FROM Rides 
GROUP BY pickup_location 
ORDER BY total_rides DESC 
LIMIT 1;
```
### 8. Total Revenue Generated
```sql
SELECT SUM(amount) AS total_revenue FROM Payments WHERE status = 'completed';
```
### 9. Identify Pending Payments
```sql
SELECT p.payment_id, p.ride_id, u.name AS rider_name, p.amount, p.payment_mode 
FROM Payments p
JOIN Rides r ON p.ride_id = r.ride_id
JOIN Users u ON r.rider_id = u.user_id
WHERE p.status = 'pending';
```
### 10. Find the Highest-Rated Driver
```sql
SELECT r.driver_id, u.name, ROUND(AVG(rt.driver_rating), 2) AS avg_rating
FROM Ratings rt
JOIN Rides r ON rt.ride_id = r.ride_id
JOIN Users u ON r.driver_id = u.user_id
GROUP BY r.driver_id, u.name
ORDER BY avg_rating DESC
LIMIT 1;
```
### 11. All Negative Feedback
```sql
SELECT r.ride_id, u.name AS rider_name, rt.rider_rating, rt.driver_rating, rt.rider_feedback, rt.driver_feedback 
FROM Ratings rt
JOIN Rides r ON rt.ride_id = r.ride_id
JOIN Users u ON r.rider_id = u.user_id
WHERE rt.rider_rating < 3.5 OR rt.driver_rating < 3.5;
```

----

## 📊 Business Strategies
### 1. User Acquisition & Market Penetration
- Implement structured referral rewards for riders and drivers.
- Establish agreements with businesses, hotels, and airports.
- Introduce discounts and seasonal promotions.
- Use AI analytics for precise digital advertising.

### 2. Driver Onboarding & Retention
- Reward high-rated drivers with performance-based incentives.
- Provide flexible working hours and fast payments.
- Offer insurance and vehicle maintenance support.
- Deploy automated allocation mechanisms to optimize ride matching.

### 3. Revenue Model & Monetization
- Implement dynamic pricing based on demand and traffic conditions.
- Introduce subscription-based ride plans for frequent commuters.
- Maintain a structured commission model for rides.
- Expand service offerings with package delivery and advertising.

### 4. Service Quality & Customer Experience Enhancement
- Use AI-powered ride matching and route optimization.
- Implement real-time tracking and emergency alerts.
- Deploy NLP-based sentiment analysis on customer feedback.
- Establish loyalty programs to encourage repeat rides.

### 5. Operational Efficiency & Cost Optimization
- Integrate IoT sensors for predictive maintenance.
- Develop an AI-powered dispatch system to reduce wait times.
- Promote electric vehicle adoption with driver incentives.
- Use geospatial analytics for demand forecasting.

### 6. Expansion & Future Scalability
- Develop a pricing model for inter-city and long-distance rides.
- Optimize ride-sharing to increase fleet utilization.
- Identify high-demand international markets for expansion.
- Leverage big data analytics for predictive insights and pricing optimization.


## Author
Your Name - [[LinkedIn Profile](https://www.linkedin.com/in/anirudhajohare/)]
