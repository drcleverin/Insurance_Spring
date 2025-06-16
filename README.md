Based on the SQL schema you shared for the `buddiesInsurance` database, below are the **Spring Boot JPA entity model classes** for all tables, with the **correct mappings** (like `@OneToMany`, `@ManyToOne`, etc.) and column definitions.

---

### ✅ 1. `Role.java`

```java
@Entity
@Table(name = "Roles")
public class Role {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer roleId;

    @Column(nullable = false, unique = true)
    private String roleName;

    @OneToMany(mappedBy = "role")
    private List<User> users;
}
```

---

### ✅ 2. `User.java`

```java
@Entity
@Table(name = "Users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer userId;

    @Column(nullable = false, unique = true)
    private String username;

    @Column(nullable = false)
    private String passwordHash;

    @Column(nullable = false, unique = true)
    private String email;

    @ManyToOne
    @JoinColumn(name = "role_id", nullable = false)
    private Role role;

    private Timestamp createdAt;

    private Timestamp lastLogin;

    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL)
    private Customer customer;
}
```

---

### ✅ 3. `Customer.java`

```java
@Entity
@Table(name = "Customers")
public class Customer {
    @Id
    private Integer customerId; // Same as userId

    @OneToOne
    @MapsId
    @JoinColumn(name = "customer_id")
    private User user;

    private String firstName;
    private String lastName;
    private LocalDate dateOfBirth;

    @Enumerated(EnumType.STRING)
    private Gender gender;

    @Column(unique = true)
    private String aadharNumber;

    private String phoneNumber;
    private String addressLine1;
    private String addressLine2;
    private String city;
    private String state;
    private String zipCode;
    private BigDecimal annualIncome;

    @OneToMany(mappedBy = "customer")
    private List<Policy> policies;

    @OneToMany(mappedBy = "customer")
    private List<Claim> claims;

    public enum Gender {
        Male, Female, Other
    }
}
```

---

### ✅ 4. `PolicyType.java`

```java
@Entity
@Table(name = "PolicyTypes")
public class PolicyType {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer policyTypeId;

    private String typeName;
    private String description;
    private Boolean isActive = true;

    @OneToMany(mappedBy = "policyType")
    private List<Policy> policies;

    @OneToMany(mappedBy = "policyType")
    private List<PolicyDescription> descriptions;
}
```

---

### ✅ 5. `PolicyDescription.java`

```java
@Entity
@Table(name = "PolicyDescriptions")
public class PolicyDescription {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer descriptionId;

    @ManyToOne
    @JoinColumn(name = "policy_type_id")
    private PolicyType policyType;

    private String name;
    private BigDecimal sumInsuredRangeMin;
    private BigDecimal sumInsuredRangeMax;
    private String premiumCalculationNotes;
    private String coveragesDescription;
    private String benefitsDescription;
    private String termsAndConditions;
}
```

---

### ✅ 6. `Policy.java`

```java
@Entity
@Table(name = "Policies")
public class Policy {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer policyId;

    private String policyNumber;

    @ManyToOne
    @JoinColumn(name = "customer_id")
    private Customer customer;

    @ManyToOne
    @JoinColumn(name = "policy_type_id")
    private PolicyType policyType;

    @ManyToOne
    @JoinColumn(name = "description_id")
    private PolicyDescription description;

    private LocalDate startDate;
    private LocalDate endDate;
    private BigDecimal premiumAmount;
    private BigDecimal sumInsured;

    @Enumerated(EnumType.STRING)
    private PolicyStatus policyStatus;

    private Timestamp issueDate;

    public enum PolicyStatus {
        Active, Lapsed, Cancelled, Expired, Pending
    }

    @OneToOne(mappedBy = "policy")
    private MotorPolicyDetails motorDetails;

    @OneToOne(mappedBy = "policy")
    private HealthPolicyDetails healthDetails;

    @OneToOne(mappedBy = "policy")
    private ProductPolicyDetails productDetails;

    @OneToMany(mappedBy = "policy")
    private List<Claim> claims;

    @OneToMany(mappedBy = "policy")
    private List<PolicyHistory> history;
}
```

---

### ✅ 7. `Vehicle.java`

```java
@Entity
@Table(name = "Vehicles")
public class Vehicle {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer vehicleId;

    private String registrationNumber;

    @Enumerated(EnumType.STRING)
    private VehicleType vehicleType;

    private String make;
    private String model;
    private Integer manufacturingYear;
    private String chassisNumber;
    private String engineNumber;
    private Integer seatingCapacity;
    private String color;

    public enum VehicleType {
        TwoWheeler, FourWheeler
    }
}
```

---

### ✅ 8. `MotorPolicyDetails.java`

```java
@Entity
@Table(name = "MotorPolicyDetails")
public class MotorPolicyDetails {
    @Id
    private Integer policyId;

    @OneToOne
    @MapsId
    @JoinColumn(name = "policy_id")
    private Policy policy;

    @ManyToOne
    @JoinColumn(name = "vehicle_id")
    private Vehicle vehicle;

    private String driverLicenseNumber;
}
```

---

### ✅ 9. `HealthPolicyDetails.java`

```java
@Entity
@Table(name = "HealthPolicyDetails")
public class HealthPolicyDetails {
    @Id
    private Integer policyId;

    @OneToOne
    @MapsId
    @JoinColumn(name = "policy_id")
    private Policy policy;

    private String medicalHistoryNotes;
    private String preExistingConditions;
    private String hospitalNetworkPreference;
}
```

---

### ✅ 10. `ProductPolicyDetails.java`

```java
@Entity
@Table(name = "ProductPolicyDetails")
public class ProductPolicyDetails {
    @Id
    private Integer policyId;

    @OneToOne
    @MapsId
    @JoinColumn(name = "policy_id")
    private Policy policy;

    private String productCategory;
    private String productName;
    private String productSerialNumber;
    private LocalDate purchaseDate;
    private String invoiceNumber;
}
```

---

### ✅ 11. `ClaimStatus.java`

```java
@Entity
@Table(name = "ClaimStatuses")
public class ClaimStatus {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer statusId;

    private String statusName;
}
```

---

### ✅ 12. `Claim.java`

```java
@Entity
@Table(name = "Claims")
public class Claim {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer claimId;

    private String claimNumber;

    @ManyToOne
    @JoinColumn(name = "policy_id")
    private Policy policy;

    @ManyToOne
    @JoinColumn(name = "customer_id")
    private Customer customer;

    private Timestamp claimDate;
    private LocalDate incidentDate;
    private String incidentDescription;
    private BigDecimal claimAmountRequested;
    private BigDecimal claimAmountApproved;

    @ManyToOne
    @JoinColumn(name = "status_id")
    private ClaimStatus status;

    private LocalDate dateResolved;

    @ManyToOne
    @JoinColumn(name = "reviewed_by_user_id")
    private User reviewedBy;
}
```

---

### ✅ 13. `ClaimDocument.java`

```java
@Entity
@Table(name = "ClaimDocuments")
public class ClaimDocument {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer documentId;

    @ManyToOne
    @JoinColumn(name = "claim_id")
    private Claim claim;

    private String documentType;
    private String filePath;
    private Timestamp uploadedAt;
}
```

---

### ✅ 14. `PolicyHistory.java`

```java
@Entity
@Table(name = "PolicyHistory")
public class PolicyHistory {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer historyId;

    @ManyToOne
    @JoinColumn(name = "policy_id")
    private Policy policy;

    private Timestamp changeDate;
    private String descriptionOfChange;

    @ManyToOne
    @JoinColumn(name = "changed_by_user_id")
    private User changedBy;
}
```

---

### ✅ 15. `ClaimHistory.java`

```java
@Entity
@Table(name = "ClaimHistory")
public class ClaimHistory {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer historyId;

    @ManyToOne
    @JoinColumn(name = "claim_id")
    private Claim claim;

    private Timestamp changeDate;
    private String descriptionOfChange;

    @ManyToOne
    @JoinColumn(name = "changed_by_user_id")
    private User changedBy;

    @ManyToOne
    @JoinColumn(name = "old_status_id")
    private ClaimStatus oldStatus;

    @ManyToOne
    @JoinColumn(name = "new_status_id")
    private ClaimStatus newStatus;
}
```

---

Would you like me to:

* ✅ Add DTOs and Mappers (e.g. `PolicyDTO`)?
* ✅ Generate full Spring REST endpoints for CRUD?
* ✅ Write sample data loader (`CommandLineRunner`) to populate test data?

Let me know what part you'd like to implement next.
