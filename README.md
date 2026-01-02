# TravelFund – Group Travel Budget & Expense Manager

## Project Overview
TravelFund is a responsive single-page web application designed to help groups manage travel budgets and split expenses fairly.  
Users can create multiple trips, add participants for each trip, record expenses, and instantly see equal-split balances. A settlement summary modal shows who should pay or receive money.

This project is built as a clean frontend prototype using **HTML, CSS, Bootstrap, and JavaScript**.

---

## Group Members
- **Farwa Batool**
- **Subham Tehseen**

---

## Features
### Core Features
- **Create Trip**
  - Add trip name and currency
  - Multiple trips supported
- **Trip Selection**
  - Switch between trips from trip list
- **Participants Management**
  - Add/remove participants per trip
  - Participant-wise paid total and balance
- **Expense Tracking**
  - Add expenses with description, amount, paid by, and category
  - Delete expenses when needed
- **Equal Split Calculation**
  - Auto total spent
  - Per person share
  - Positive/negative balance view
- **Settlement Summary Modal**
  - Shows Paid, Should Pay, and Balance for each participant

### UI / UX Features
- Fully responsive layout (mobile, tablet, desktop)
- AI-inspired dark glass UI theme
- Smooth Bootstrap components (carousel, modal, tables, cards)

---

## Tools & Technologies
- **HTML5**
- **CSS3 (Custom AI-theme styling)**
- **Bootstrap 5.3**
- **JavaScript (Vanilla JS)**

---

## Project Structure
TravelFund/
│── index.html
│── styles.css
│── app.js
└── README.md

<br>
import { useMemo, useState } from "react";
import Navbar from "./components/Navbar";
import Hero from "./components/Hero";
import About from "./components/About";
import TripPanel from "./components/TripPanel";
import Participants from "./components/Participants";
import Expenses from "./components/Expenses";
import SettlementModal from "./components/SettlementModal";

function makeId(prefix) {
  return `${prefix}_${Date.now()}_${Math.random().toString(16).slice(2)}`;
}

export default function App() {
  const [trips, setTrips] = useState([]); // {id,name,currency,participants[],expenses[]}
  const [activeTripId, setActiveTripId] = useState(null);

  const activeTrip = useMemo(
    () => trips.find((t) => t.id === activeTripId) || null,
    [trips, activeTripId]
  );

  const balances = useMemo(() => {
    if (!activeTrip) {
      return { peopleCount: 0, totalSpent: 0, perPerson: 0, rows: [] };
    }
    const peopleCount = activeTrip.participants.length;
    const totalSpent = activeTrip.expenses.reduce((sum, e) => sum + e.amount, 0);
    const perPerson = peopleCount > 0 ? totalSpent / peopleCount : 0;

    const rows = activeTrip.participants.map((p) => {
      const paidTotal = activeTrip.expenses
        .filter((e) => e.paidBy === p.id)
        .reduce((sum, e) => sum + e.amount, 0);

      const shouldPay = perPerson;
      const balance = paidTotal - shouldPay;

      return { id: p.id, name: p.name, paidTotal, shouldPay, balance };
    });

    return { peopleCount, totalSpent, perPerson, rows };
  }, [activeTrip]);
