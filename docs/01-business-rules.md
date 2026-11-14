# Business Rules & Domain Model — Inbound Logistics TMS

This document outlines the core domain actors, operational constraints, and domain entities governing the inbound logistics and freight transport management system.

---

## 1. Domain Actors

* **Supplier (Remetente / Fornecedor):** Origin entity responsible for manufacturing or storing goods, generating commercial invoices, and releasing cargo for scheduled pickups.
* **Carrier (Transportadora):** Logistics provider operating a fleet of vehicles and drivers. Responsible for executing the physical transport from Suppliers to Distribution Centers.
* **Distribution Center (Destinatário / CD):** Central warehouse receiving, inspecting, and unloading inbound freight. Manages dock scheduling and warehouse inventory intake.
* **System Operator / Admin:** Internal user managing carrier contracts, overrides, system audits, and exception flows.

---

## 2. Core Entities & Attributes

1. **Freight (Carga / Ordem de Transporte):**
   * Unique identifier (`id`, UUIDv4)
   * Invoice reference number (`invoice_key`)
   * Weight (`gross_weight_kg`), Volume (`volume_m3`), and Declared Value (`declared_value`)
   * Origin Supplier ID & Destination DC ID
   * Assigned Carrier ID and Vehicle/Driver allocation

2. **Vehicle (Veículo):**
   * License plate (`plate`), model, maximum weight capacity (`max_weight_kg`), and volumetric limit (`max_volume_m3`).
   * Current status (`AVAILABLE`, `IN_TRANSIT`, `MAINTENANCE`).

3. **Dock Schedule (Janela de Descarga):**
   * Time slot reserved at the Distribution Center to prevent bottlenecks during cargo arrival.

---

## 3. Key Business Constraints & Validations

1. **Capacity Enforcement:** A carrier vehicle cannot be allocated to a freight order if the cumulative gross weight or volume exceeds the vehicle's maximum limits (`sum(freight.weight) <= vehicle.max_weight`).
2. **Mandatory Documentation:** A freight cannot transition to `DISPATCHED` without a valid tax invoice reference (`invoice_key`) and an assigned active driver.
3. **Immutability:** Once a freight reaches `IN_TRANSIT`, origin and weight fields become strictly immutable to protect financial and fiscal audits.
