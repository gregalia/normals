# Microservices Architecture

## Core Services

1. **Patient Service** - Demographics, medical history, insurance information
2. **Scheduling Service** - Appointments, provider calendars, room management
3. **Clinical Service** - Exam data, prescriptions, diagnostic images, eye measurements
4. **Billing Service** - Charges, insurance claims, payment processing
5. **Inventory Service** - Frames, contact lenses, equipment tracking
6. **Notification Service** - Appointment reminders, follow-up alerts, SMS/email
7. **Audit Service** - HIPAA compliance logging, access tracking, security events
8. **Integration Service** - Insurance verification, lab orders, supplier APIs

## Event Topics (Kafka)

```txt
patient.registered
patient.updated
appointment.scheduled
appointment.cancelled
appointment.rescheduled
exam.started
exam.completed
prescription.created
prescription.updated
claim.submitted
claim.processed
inventory.depleted
inventory.restocked
audit.access.logged
notification.sent
```

## Technical Challenges

### Real-time Requirements

- **Patient queue** updates across multiple workstations
- **Room status** (occupied, ready, cleaning) for workflow optimization
- **Inventory alerts** when popular frame styles run low
- **Insurance verification** during check-in process

### Data Consistency

- **Prescription accuracy** - Critical for patient safety
- **Billing integrity** - Charges must match services rendered
- **Appointment conflicts** - Prevent double-booking resources
- **Inventory tracking** - Accurate stock levels across sales channels

### Integration Complexity

- **Insurance APIs** - Real-time eligibility and claims submission
- **Lab integrations** - Lens orders and tracking
- **Supplier APIs** - Frame catalogs and ordering
- **Payment processing** - Credit cards, HSA/FSA accounts
