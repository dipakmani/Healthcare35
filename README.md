import pandas as pd
import random
from faker import Faker

fake = Faker()

# Parameters
n_visits = 500000   # total rows
n_patients = 50000  # unique patients
n_doctors = 100     # unique doctors

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


# --------------------------
# Create fixed pool of patients
# --------------------------
patients = []
for pid in range(1, n_patients+1):
    first = fake.first_name()
    last = fake.last_name()
    patient = {
        "PatientID": pid,
        "FirstName": first,
        "LastName": last,
        "PatientFullName": f"{first} {last}",
        "Gender": random_gender(),
        "DOB": fake.date_of_birth(minimum_age=1, maximum_age=90),
        "City": fake.city(),
        "State": fake.state(),
        "Country": fake.country(),
        "Age": random.randint(1, 90),
        "BloodGroup": random_blood_group(),
    }
    patients.append(patient)

patients_df = pd.DataFrame(patients)


# --------------------------
# Create fixed pool of doctors
# --------------------------
doctors = []
for did in range(1, n_doctors+1):
    doctors.append({
        "DoctorID": did,
        "DoctorName": fake.name(),
        "DoctorGender": random_gender(),
        "Specialty": random.choice(["Cardiology", "Orthopedics", "Neurology", "Oncology", "General Medicine"]),
        "DepartmentID": random.randint(1, 50),
        "ExperienceYears": random.randint(1, 40),
        "DoctorFees": random.randint(200, 800),
        "DoctorEmail": fake.email()
    })

doctors_df = pd.DataFrame(doctors)


# --------------------------
# Generate visits (fact table)
# --------------------------
records = []
for vid in range(1, n_visits+1):
    # Pick a patient (revisit possible)
    patient = patients_df.sample(1).iloc[0]
    doctor = doctors_df.sample(1).iloc[0]
    
    admission_date = fake.date_between(start_date="-2y", end_date="today")
    discharge_date = fake.date_between(start_date=admission_date, end_date="today")
    
    record = {
        "VisitID": vid,
        
        # Patient info
        **patient.to_dict(),
        "AdmissionDate": admission_date,
        "DischargeDate": discharge_date,
        "TotalBillingAmount": round(random.uniform(5000, 200000), 2),
        
        # Doctor info
        **doctor.to_dict(),
        
        # Hospital info
        "HospitalID": random.randint(1, 10),
        "HospitalName": fake.company(),
        "HospitalCity": fake.city(),
        "HospitalState": fake.state(),
        "HospitalCountry": fake.country(),
        "HospitalCapacity": random.randint(50, 1000),
        "IsGovtHospital": random.choice(["Yes", "No"]),
        
        # Department info
        "DepartmentName": random.choice(["Cardiology", "Orthopedics", "Neurology", "Oncology", "General Medicine"]),
        "NoOfBeds": random.randint(5, 200),
        "ContactExtension": random.randint(1000, 9999),
        
        # Insurance info
        "InsuranceID": random.randint(10000, 99999),
        "InsuranceProvider": fake.company(),
        "PolicyType": random_policy_type(),
        "CoverageAmount": random.randint(50000, 1000000),
        "PolicyStartDate": fake.date_between(start_date="-5y", end_date="-1y"),
        "PolicyEndDate": fake.date_between(start_date="-1y", end_date="+5y"),
        "PolicyStatus": random.choice(["Active", "Expired", "Pending"]),
        
        # Payment info
        "PaymentID": random.randint(100000, 999999),
        "PaymentMode": random_payment_mode(),
        "PaymentStatus": random_payment_status(),
        "BillingAmount": round(random.uniform(1000, 100000), 2),
        "TaxAmount": round(random.uniform(100, 5000), 2),
        "DiscountApplied": round(random.uniform(0, 5000), 2),
        "NetAmount": round(random.uniform(1000, 200000), 2),
        
        # Diagnosis info
        "DiagnosisID": random.randint(100, 999),
        "DiagnosisName": random.choice(["Diabetes", "Hypertension", "Cancer", "Fracture", "Migraine"]),
        "ICD10Code": fake.bothify(text="ICD-10-??##"),
        "Severity": random.choice(["Mild", "Moderate", "Severe"]),
        "DiagnosisCategory": random_diagnosis_category(),
        "ChronicFlag": random.choice(["Yes", "No"]),
        "DiagnosisNotes": fake.sentence(nb_words=6),
        
        # Medication plan
        "MedicationPlanID": random.randint(1000, 9999),
        "MedicationName": random.choice(["Paracetamol", "Metformin", "Aspirin", "Atorvastatin", "Omeprazole"]),
        "Dosage": random.choice(["250mg", "500mg", "10mg", "20mg", "100mg"]),
        "Frequency": random.choice(["Once Daily", "Twice Daily", "Thrice Daily"]),
        "DurationDays": random.randint(1, 365),
        "RouteOfAdministration": random.choice(["Oral", "IV", "IM", "Topical"]),
        "MedicationNotes": fake.sentence(nb_words=5),
        
        # Room info
        "RoomID": random.randint(1, 500),
        "RoomNumber": random.randint(100, 999),
        "Ward": random.choice(["Ward A", "Ward B", "Ward C"]),
        "Floor": random.randint(1, 10),
        "RoomType": random_room_type(),
        "RoomCapacity": random.randint(1, 6),
        "ChargesPerDay": round(random.uniform(500, 10000), 2),
        
        # Referral info
        "ReferralID": random.randint(1000, 9999),
        "ReferralSource": random_referral_source(),
        "ReferredBy": fake.name(),
        "ReferralReason": fake.sentence(nb_words=5),
        "ReferralDate": fake.date_between(start_date="-2y", end_date="today"),
        "ReferralStatus": random.choice(["Accepted", "Pending", "Rejected"]),
        
        # Staff info
        "StaffID": random.randint(1000, 9999),
        "StaffName": fake.name(),
        "StaffRole": random_role(),
        "StaffDepartmentID": random.randint(1, 50),
        "ShiftTiming": random.choice(["Morning", "Evening", "Night"]),
        "StaffContactNumber": fake.phone_number(),
        "StaffEmail": fake.email()
    }
    
    records.append(record)

# Create DataFrame
df = pd.DataFrame(records)

# Save to CSV
df.to_csv("healthcare_visits.csv", index=False)

print("✅ CSV file 'healthcare_visits.csv' with 5 lakh visit records generated successfully!")
