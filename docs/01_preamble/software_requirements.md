# Software Requirements Document (SRD)

NOTE: THIS IS NOT A DELIVERABLE. IT IS TO COME AT A COMMON UNDERSTANDING

Project Title: AI-Enhanced Mobility Platform for MobilityCorp

Prepared For: MobilityCorp Executive &amp; Product Teams

Prepared By: BRINUS – Prashant, Rafael, Harish, Khaled, Joao

Date: October 16, 2025

## 1. Introduction

### 1.1 Purpose

This document outlines the software requirements for a new architecture for MobilityCorp’s mobility rental platform, incorporating AI functionality where appropriate. The goal is to address key business challenges, improve customer satisfaction, and optimize fleet operations.

### 1.2 Scope

MobilityCorp provides short-term rentals for last-mile transport including electric scooters, eBikes, electric cars, and vans. The platform operates in multiple urban and suburban locations across the EU and must support multi-language and multi-currency capabilities.

## 2. Business Overview

MobilityCorp enables customers to rent vehicles via a mobile application. The fleet includes:

- Electric Scooters
- eBikes
- Electric Cars and Vans

### 2.1 Booking Rules

- Cars and Vans:
  - Bookable up to 7 days in advance
  - Fixed duration rentals

- Scooters and Bikes:
  - Bookable up to 30 minutes in advance
  - Open-ended rentals (up to 12 hours)

### 2.2 Payment Model

- Per-minute billing
- Fines for:
  - Late returns
  - Returning vehicles to incorrect locations

## 3. Functional Requirements

### 3.1 Vehicle Management

- GPS tracking for all vehicles
- Remote unlock capability
- NFC-based smartphone app for locking/unlocking
- Remote disable capability for cars and vans

### 3.2 Booking System

- Support for advance and short-term bookings
- Real-time availability tracking
- Duration-based booking logic per vehicle type

### 3.3 Return Process

- Mandatory return to designated parking spots
- Photo proof submission by customers
- EVs (cars and vans) must be plugged into chargers
- Customer feedback collection (including fault reporting)

### 3.4 Charging and Distribution

- Battery swap alerts for bikes and scooters
- Staff routing for battery swaps and vehicle redistribution
- Identification of high-demand locations

## 4. Business Challenges

- Vehicles are often not available where customers need them
  - Need to predict demand and anticipate customer needs

- EVs frequently run out of charge
  - Need to prioritize battery swaps and charging schedules

- Most usage is ad-hoc
  - Desire to increase regular usage (e.g., daily commutes)

