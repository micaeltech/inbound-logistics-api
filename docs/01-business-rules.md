# Business Rules & Domain Specification — Inbound Logistics TMS

## 1. Domain Overview

The system manages the food and goods supply chain transport from origin (Producers / Industries) to destination (Large Retail Distribution Centers). The application controls participant registration, vehicle fleet, maintenance logs, route planning, and the freight order lifecycle.

---

## 2. Main Entities and Actors

### 2.1 Origin Point (Supplier / Industry)
* **2.1.1** Must have a unique identifier, Corporate Name (`Razão Social`), Tax ID (`CNPJ` - unique), State Registration (`Inscrição Estadual`), Contact Phone, and Full Address (Street, Number, Neighborhood, City, State, ZIP Code).
* **2.1.2** An Origin Point is responsible for issuing the goods shipment request to a Distribution Center.

### 2.2 Distribution Center (DC - Destination)
* **2.2.1** Must have a unique identifier, Retail Chain Name (e.g., CD Assaí, CD Carrefour), Tax ID (`CNPJ` - unique), Daily Maximum Receiving Capacity (in tons or volume), and Full Address.
* **2.2.2** The DC operates with specific time slots/windows to receive trucks (Unloading Dock).

### 2.3 Driver
* **2.3.1** Must have a unique identifier, Full Name, Individual Tax ID (`CPF` - unique), Driver's License Number (`CNH` - unique), License Category (e.g., `C`, `D`, `E`), and Operational Status (`AVAILABLE`, `IN_TRANSIT`, `INACTIVE`).
* **2.3.2 Validation Rule:** A driver can only be allocated to a trip if their license category is compatible with the required truck type.
* **2.3.3 Availability Rule:** A driver with status `IN_TRANSIT` or `INACTIVE` cannot accept new freight orders.

### 2.4 Vehicle (Truck)
* **2.4.1** Must have a License Plate (unique), Model, Brand, Year, Maximum Load Capacity (in kg/tons), Body Type (`REFRIGERATED`, `CLOSED_BOX`, `BULK_CARRIER`, `SIDER`), and Vehicle Status (`AVAILABLE`, `IN_TRANSIT`, `UNDER_MAINTENANCE`, `INACTIVE`).
* **2.4.2 Maintenance Rule:** If the vehicle is flagged with status `UNDER_MAINTENANCE`, it automatically blocks any attempt to bind it to a new route or freight order.

### 2.5 Maintenance / Repair Record
* **2.5.1** Must have a unique identifier, Start Date, Estimated Completion Date, Actual Completion Date, Service Cost, Defect/Repair Description, and Maintenance Type (`PREVENTIVE` or `CORRECTIVE`).
* **2.5.2 Vehicle Side Effect:** Upon opening a Maintenance Record with status `IN_PROGRESS`, the associated truck's status must be obligatorily updated to `UNDER_MAINTENANCE`.
* **2.5.3** Upon completing the maintenance (`COMPLETED`), the truck's status returns to `AVAILABLE`.

---

## 3. Cargo and Freight Order Rules

### 3.1 Cargo / Merchandise
* **3.1.1** Must have a Description, Cargo Type (`PERISHABLE_REFRIGERATED`, `PERISHABLE_DRY`, `NON_PERISHABLE`), Total Weight (`kg`), and Declared Value (`BRL`).
* **3.1.2 Refrigeration Compatibility Rule:** Cargo of type `PERISHABLE_REFRIGERATED` strictly requires that the associated vehicle has a `REFRIGERATED` body type.

### 3.2 Freight Order (Transport Request)
* **3.2.1** A Freight Order links: 1 Origin Point, 1 Distribution Center (Destination), 1 Cargo/Merchandise, and holds a Request Date.
* **3.2.2** The Order follows a strict State Machine (Lifecycle):
  * `REQUESTED` — Created by the Supplier, awaiting vehicle/driver allocation.
  * `AWAITING_BOARDING` — Vehicle and driver allocated, awaiting departure from origin.
  * `IN_TRANSIT` — Cargo in transport on the way to the DC.
  * `DELIVERED` — Cargo received and inspected at the DC.
  * `CANCELLED` — Cancellation requested prior to boarding.

---

## 4. Route, Allocation, and Trip Rules

### 4.1 Route and Trip
* **4.1.1** A Trip links: 1 Freight Order, 1 Truck, 1 Driver, Actual Departure Date/Time, and Estimated/Actual Arrival Date/Time.
* **4.1.2 Weight Limit Rule (Capacity):** The Total Weight of the order's cargo cannot exceed the Maximum Load Capacity of the allocated truck (`cargo.totalWeight <= truck.maxLoadCapacity`).
* **4.1.3 Allocation Uniqueness Rule:** 1 Truck can only be associated with 1 active Trip (`IN_TRANSIT` or `AWAITING_BOARDING`) at a time.
