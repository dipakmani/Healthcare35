import pandas as pd
import numpy as np
from faker import Faker
import random

# Initialize Faker
fake = Faker()

# Number of records
n = 500000  

# Helper functions
def random_blood_group():
    return random.choice(["A+", "A-", "B+", "B-", "AB+", "AB-", "O+", "O-"])

def random_gender():
    return random.choice(["Male", "Female"])

def random_room_type():
    return random.choice(["ICU", "General", "Private"])

def random_role():
    return random.choice(["Nurse", "Technician", "Admin"])

def random_payment_mode():
    return random.choice(["Cash", "Card", "UPI", "Insurance"])

def random_payment_status():
    return random.choice(["Pending", "Completed", "Failed"])

def random_referral_source():
    return random.choice(["Doctor", "Insurance", "Online", "Other"])

def random_diagnosis_category():
    return random.choice(["Cardiology", "Neurology", "Orthopedic", "Oncology", "General"])

def random_policy_type():
    return random.choice(["Health", "Life", "Accident", "Critical Illness"])

# Generate dataset
data = {
    # Patient Info
    "PatientID": [i for i in range(1, n+1)],
    "FirstName": [fake.first_name() for _ in range(n)],
    "LastName": [fake.last_name() for _ in range(n)],
    "Gender": [random_gender() for _ in range(n)],
    "DOB": [fake.date_of_birth(minimum_age=1, maximum_age=90) for _ in range(n)],
    "City": [fake.city() for _ in range(n)],
    "State": [fake.state() for _ in range(n)],
    "Country": [fake.country() for _ in range(n)],
    "Age": [random.randint(1, 90) for _ in range(n)],
    "BloodGroup": [random_blood_group() for _ in range(n)],
    "AdmissionDate": [fake.date_between(start_date="-2y", end_date="today") for _ in range(n)],
    "DischargeDate": [fake.date_between(start_date="-1y", end_date="today") for _ in range(n)],
    "TotalBillingAmount": [round(random.uniform(5000, 200000), 2) for _ in range(n)],

    # Doctor Info
    "DoctorID": [random.randint(1000, 9999) for _ in range(n)],
    "DoctorName": [fake.name() for _ in range(n)],
    "DoctorGender": [random_gender() for _ in range(n)],   # Only Male/Female
    "Specialty": [random.choice(["Cardiology", "Orthopedics", "Neurology", "Oncology", "General Medicine"]) for _ in range(n)],
    "DepartmentID": [random.randint(1, 50) for _ in range(n)],
    "ExperienceYears": [random.randint(1, 40) for _ in range(n)],
    "DoctorFees": [random.randint(200, 800) for _ in range(n)],
    "DoctorEmail": [fake.email() for _ in range(n)],

    # Hospital Info
    "HospitalID": [random.randint(1, 100) for _ in range(n)],
    "HospitalName": [fake.company() for _ in range(n)],
    "HospitalCity": [fake.city() for _ in range(n)],
    "HospitalState": [fake.state() for _ in range(n)],
    "HospitalCountry": [fake.country() for _ in range(n)],
    "HospitalCapacity": [random.randint(50, 1000) for _ in range(n)],
    "IsGovtHospital": [random.choice(["Yes", "No"]) for _ in range(n)],

    # Department Info
    "DepartmentName": [random.choice(["Cardiology", "Orthopedics", "Neurology", "Oncology", "General Medicine"]) for _ in range(n)],
    "NoOfBeds": [random.randint(5, 200) for _ in range(n)],
    "ContactExtension": [random.randint(1000, 9999) for _ in range(n)],

    # Insurance Info
    "InsuranceID": [random.randint(10000, 99999) for _ in range(n)],
    "InsuranceProvider": [fake.company() for _ in range(n)],
    "PolicyType": [random_policy_type() for _ in range(n)],
    "CoverageAmount": [random.randint(50000, 1000000) for _ in range(n)],
    "PolicyStartDate": [fake.date_between(start_date="-5y", end_date="-1y") for _ in range(n)],
    "PolicyEndDate": [fake.date_between(start_date="-1y", end_date="+5y") for _ in range(n)],
    "PolicyStatus": [random.choice(["Active", "Expired", "Pending"]) for _ in range(n)],

    # Payment Info
    "PaymentID": [random.randint(100000, 999999) for _ in range(n)],
    "PaymentMode": [random_payment_mode() for _ in range(n)],
    "PaymentStatus": [random_payment_status() for _ in range(n)],
    "BillingAmount": [round(random.uniform(1000, 100000), 2) for _ in range(n)],
    "TaxAmount": [round(random.uniform(100, 5000), 2) for _ in range(n)],
    "DiscountApplied": [round(random.uniform(0, 5000), 2) for _ in range(n)],
    "NetAmount": [round(random.uniform(1000, 200000), 2) for _ in range(n)],

    # Diagnosis Info
    "DiagnosisID": [random.randint(100, 999) for _ in range(n)],
    "DiagnosisName": [random.choice(["Diabetes", "Hypertension", "Cancer", "Fracture", "Migraine"]) for _ in range(n)],
    "ICD10Code": [fake.bothify(text="ICD-10-??##") for _ in range(n)],
    "Severity": [random.choice(["Mild", "Moderate", "Severe"]) for _ in range(n)],
    "DiagnosisCategory": [random_diagnosis_category() for _ in range(n)],
    "ChronicFlag": [random.choice(["Yes", "No"]) for _ in range(n)],
    "DiagnosisNotes": [fake.sentence(nb_words=6) for _ in range(n)],

    # Medication Plan
    "MedicationPlanID": [random.randint(1000, 9999) for _ in range(n)],
    "MedicationName": [random.choice(["Paracetamol", "Metformin", "Aspirin", "Atorvastatin", "Omeprazole"]) for _ in range(n)],
    "Dosage": [random.choice(["250mg", "500mg", "10mg", "20mg", "100mg"]) for _ in range(n)],
    "Frequency": [random.choice(["Once Daily", "Twice Daily", "Thrice Daily"]) for _ in range(n)],
    "DurationDays": [random.randint(1, 365) for _ in range(n)],
    "RouteOfAdministration": [random.choice(["Oral", "IV", "IM", "Topical"]) for _ in range(n)],
    "MedicationNotes": [fake.sentence(nb_words=5) for _ in range(n)],

    # Room Info
    "RoomID": [random.randint(1, 500) for _ in range(n)],
    "RoomNumber": [random.randint(100, 999) for _ in range(n)],
    "Ward": [random.choice(["Ward A", "Ward B", "Ward C"]) for _ in range(n)],
    "Floor": [random.randint(1, 10) for _ in range(n)],
    "RoomType": [random_room_type() for _ in range(n)],
    "RoomCapacity": [random.randint(1, 6) for _ in range(n)],
    "ChargesPerDay": [round(random.uniform(500, 10000), 2) for _ in range(n)],

    # Referral Info
    "ReferralID": [random.randint(1000, 9999) for _ in range(n)],
    "ReferralSource": [random_referral_source() for _ in range(n)],
    "ReferredBy": [fake.name() for _ in range(n)],
    "ReferralReason": [fake.sentence(nb_words=5) for _ in range(n)],
    "ReferralDate": [fake.date_between(start_date="-2y", end_date="today") for _ in range(n)],
    "ReferralStatus": [random.choice(["Accepted", "Pending", "Rejected"]) for _ in range(n)],

    # Staff Info
    "StaffID": [random.randint(1000, 9999) for _ in range(n)],
    "StaffName": [fake.name() for _ in range(n)],
    "StaffRole": [random_role() for _ in range(n)],
    "StaffDepartmentID": [random.randint(1, 50) for _ in range(n)],
    "ShiftTiming": [random.choice(["Morning", "Evening", "Night"]) for _ in range(n)],
    "StaffContactNumber": [fake.phone_number() for _ in range(n)],
    "StaffEmail": [fake.email() for _ in range(n)]
}

# Convert to DataFrame
df = pd.DataFrame(data)

# Save to CSV
df.to_csv("healthcare_dataset.csv", index=False)

print("✅ CSV file 'healthcare_dataset.csv' with 5 lakh records generated successfully (only Male/Female genders)!")
