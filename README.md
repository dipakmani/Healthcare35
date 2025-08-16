import pandas as pd
import random
from faker import Faker

fake = Faker()

# Number of records (visits)
n = 500000  

# Number of unique patients
num_patients = 100000   # e.g., 1 lakh patients, each may have multiple visits

# Number of doctors fixed
num_doctors = 50  

# --------------------------
# Generate Patients (fixed details)
# --------------------------
patients = []
for pid in range(1, num_patients + 1):
    first = fake.first_name()
    last = fake.last_name()
    patients.append({
        "PatientID": pid,
        "FirstName": first,
        "LastName": last,
        "FullName": f"{first} {last}",
        "Gender": random.choice(["Male", "Female"]),
        "DOB": fake.date_of_birth(minimum_age=1, maximum_age=90),
        "City": fake.city(),
        "State": fake.state(),
        "Country": fake.country(),
        "Age": random.randint(1, 90),
        "BloodGroup": random.choice(["A+", "A-", "B+", "B-", "AB+", "AB-", "O+", "O-"]),
    })

patients_df = pd.DataFrame(patients)

# --------------------------
# Generate Doctors (only 50)
# --------------------------
doctors = []
for did in range(1, num_doctors + 1):
    doctors.append({
        "DoctorID": did,
        "DoctorName": fake.name(),
        "DoctorGender": random.choice(["Male", "Female"]),
        "Specialty": random.choice(["Cardiology", "Orthopedics", "Neurology", "Oncology", "General Medicine"]),
        "DepartmentID": random.randint(1, 50),
        "ExperienceYears": random.randint(1, 40),
        "DoctorFees": random.randint(200, 800),
        "DoctorEmail": fake.email(),
    })

doctors_df = pd.DataFrame(doctors)

# --------------------------
# Generate Visits (5 lakh rows)
# --------------------------
visits = []
for vid in range(1, n + 1):
    # Pick a patient and doctor
    patient = patients_df.sample(1).iloc[0]
    doctor = doctors_df.sample(1).iloc[0]

    admission_date = fake.date_between(start_date="-2y", end_date="today")
    discharge_date = fake.date_between(start_date=admission_date, end_date="today")

    visits.append({
        "VisitID": vid,

        # Patient Info (repeated correctly)
        "PatientID": patient.PatientID,
        "FirstName": patient.FirstName,
        "LastName": patient.LastName,
        "FullName": patient.FullName,
        "Gender": patient.Gender,
        "DOB": patient.DOB,
        "City": patient.City,
        "State": patient.State,
        "Country": patient.Country,
        "Age": patient.Age,
        "BloodGroup": patient.BloodGroup,
        "AdmissionDate": admission_date,
        "DischargeDate": discharge_date,
        "TotalBillingAmount": round(random.uniform(5000, 200000), 2),

        # Doctor Info (only from 50)
        "DoctorID": doctor.DoctorID,
        "DoctorName": doctor.DoctorName,
        "DoctorGender": doctor.DoctorGender,
        "Specialty": doctor.Specialty,
        "DepartmentID": doctor.DepartmentID,
        "ExperienceYears": doctor.ExperienceYears,
        "DoctorFees": doctor.DoctorFees,
        "DoctorEmail": doctor.DoctorEmail,

        # Hospital Info
        "HospitalID": random.randint(1, 10),
        "HospitalName": fake.company(),
        "HospitalCity": fake.city(),
        "HospitalState": fake.state(),
        "HospitalCountry": fake.country(),
        "HospitalCapacity": random.randint(50, 1000),
        "IsGovtHospital": random.choice(["Yes", "No"]),

        # Department Info
        "DepartmentName": random.choice(["Cardiology", "Orthopedics", "Neurology", "Oncology", "General Medicine"]),
        "NoOfBeds": random.randint(5, 200),
        "ContactExtension": random.randint(1000, 9999),

        # Insurance Info
        "InsuranceID": random.randint(10000, 99999),
        "InsuranceProvider": fake.company(),
        "PolicyType": random.choice(["Health", "Life", "Accident", "Critical Illness"]),
        "CoverageAmount": random.randint(50000, 1000000),
        "PolicyStartDate": fake.date_between(start_date="-5y", end_date="-1y"),
        "PolicyEndDate": fake.date_between(start_date="-1y", end_date="+5y"),
        "PolicyStatus": random.choice(["Active", "Expired", "Pending"]),

        # Payment Info
        "PaymentID": random.randint(100000, 999999),
        "PaymentMode": random.choice(["Cash", "Card", "UPI", "Insurance"]),
        "PaymentStatus": random.choice(["Pending", "Completed", "Failed"]),
        "BillingAmount": round(random.uniform(1000, 100000), 2),
        "TaxAmount": round(random.uniform(100, 5000), 2),
        "DiscountApplied": round(random.uniform(0, 5000), 2),
        "NetAmount": round(random.uniform(1000, 200000), 2),

        # Diagnosis Info
        "DiagnosisID": random.randint(100, 999),
        "DiagnosisName": random.choice(["Diabetes", "Hypertension", "Cancer", "Fracture", "Migraine"]),
        "ICD10Code": fake.bothify(text="ICD-10-??##"),
        "Severity": random.choice(["Mild", "Moderate", "Severe"]),
        "DiagnosisCategory": random.choice(["Cardiology", "Neurology", "Orthopedic", "Oncology", "General"]),
        "ChronicFlag": random.choice(["Yes", "No"]),
        "DiagnosisNotes": fake.sentence(nb_words=6),

        # Medication Plan
        "MedicationPlanID": random.randint(1000, 9999),
        "MedicationName": random.choice(["Paracetamol", "Metformin", "Aspirin", "Atorvastatin", "Omeprazole"]),
        "Dosage": random.choice(["250mg", "500mg", "10mg", "20mg", "100mg"]),
        "Frequency": random.choice(["Once Daily", "Twice Daily", "Thrice Daily"]),
        "DurationDays": random.randint(1, 365),
        "RouteOfAdministration": random.choice(["Oral", "IV", "IM", "Topical"]),
        "MedicationNotes": fake.sentence(nb_words=5),

        # Room Info
        "RoomID": random.randint(1, 500),
        "RoomNumber": random.randint(100, 999),
        "Ward": random.choice(["Ward A", "Ward B", "Ward C"]),
        "Floor": random.randint(1, 10),
        "RoomType": random.choice(["ICU", "General", "Private"]),
        "RoomCapacity": random.randint(1, 6),
        "ChargesPerDay": round(random.uniform(500, 10000), 2),

        # Referral Info
        "ReferralID": random.randint(1000, 9999),
        "ReferralSource": random.choice(["Doctor", "Insurance", "Online", "Other"]),
        "ReferredBy": fake.name(),
        "ReferralReason": fake.sentence(nb_words=5),
        "ReferralDate": fake.date_between(start_date="-2y", end_date="today"),
        "ReferralStatus": random.choice(["Accepted", "Pending", "Rejected"]),

        # Staff Info
        "StaffID": random.randint(1000, 9999),
        "StaffName": fake.name(),
        "StaffRole": random.choice(["Nurse", "Technician", "Admin"]),
        "StaffDepartmentID": random.randint(1, 50),
        "ShiftTiming": random.choice(["Morning", "Evening", "Night"]),
        "StaffContactNumber": fake.phone_number(),
        "StaffEmail": fake.email()
    })

# Create DataFrame
df = pd.DataFrame(visits)

# Save to CSV
df.to_csv("healthcare_dataset.csv", index=False)

print("✅ CSV file 'healthcare_dataset.csv' with 5 lakh visit records generated successfully!")
