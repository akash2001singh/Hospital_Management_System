# Hospital_Management_System


import sqlite3
from tkinter import *
from tkinter import ttk, messagebox

# Database Connection
conn = sqlite3.connect('hospital.db')
cursor = conn.cursor()

# Creating tables if not exists
cursor.execute("""
CREATE TABLE IF NOT EXISTS patients (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    age INTEGER,
    gender TEXT,
    contact TEXT,
    address TEXT
)
""")

cursor.execute("""
CREATE TABLE IF NOT EXISTS doctors (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    specialization TEXT,
    contact TEXT
)
""")

cursor.execute("""
CREATE TABLE IF NOT EXISTS appointments (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    patient_id INTEGER,
    doctor_id INTEGER,
    date TEXT,
    time TEXT,
    FOREIGN KEY (patient_id) REFERENCES patients(id),
    FOREIGN KEY (doctor_id) REFERENCES doctors(id)
)
""")
conn.commit()

# GUI Setup
root = Tk()
root.title("Hospital Management System")
root.geometry("900x600")

notebook = ttk.Notebook(root)
notebook.pack(fill='both', expand=True)

# Patient Management Frame
patient_frame = Frame(notebook)
notebook.add(patient_frame, text="Patients")

def add_patient():
    name = name_entry.get()
    age = age_entry.get()
    gender = gender_entry.get()
    contact = contact_entry.get()
    address = address_entry.get()
    cursor.execute("INSERT INTO patients (name, age, gender, contact, address) VALUES (?, ?, ?, ?, ?)",
                   (name, age, gender, contact, address))
    conn.commit()
    messagebox.showinfo("Success", "Patient added successfully")

def view_patients():
    cursor.execute("SELECT * FROM patients")
    records = cursor.fetchall()
    for record in records:
        patient_list.insert("", END, values=record)

Label(patient_frame, text="Patient Name").grid(row=0, column=0)
Label(patient_frame, text="Age").grid(row=1, column=0)
Label(patient_frame, text="Gender").grid(row=2, column=0)
Label(patient_frame, text="Contact").grid(row=3, column=0)
Label(patient_frame, text="Address").grid(row=4, column=0)

name_entry = Entry(patient_frame)
age_entry = Entry(patient_frame)
gender_entry = Entry(patient_frame)
contact_entry = Entry(patient_frame)
address_entry = Entry(patient_frame)

name_entry.grid(row=0, column=1)
age_entry.grid(row=1, column=1)
gender_entry.grid(row=2, column=1)
contact_entry.grid(row=3, column=1)
address_entry.grid(row=4, column=1)

Button(patient_frame, text="Add Patient", command=add_patient).grid(row=5, column=0)
Button(patient_frame, text="View Patients", command=view_patients).grid(row=5, column=1)

patient_list = ttk.Treeview(patient_frame, columns=("ID", "Name", "Age", "Gender", "Contact", "Address"), show='headings')
for col in ("ID", "Name", "Age", "Gender", "Contact", "Address"):
    patient_list.heading(col, text=col)
patient_list.grid(row=6, column=0, columnspan=2)

# Doctor Management Frame
doctor_frame = Frame(notebook)
notebook.add(doctor_frame, text="Doctors")

def add_doctor():
    name = doctor_name_entry.get()
    specialization = specialization_entry.get()
    contact = doctor_contact_entry.get()
    cursor.execute("INSERT INTO doctors (name, specialization, contact) VALUES (?, ?, ?)",
                   (name, specialization, contact))
    conn.commit()
    messagebox.showinfo("Success", "Doctor added successfully")

def view_doctors():
    cursor.execute("SELECT * FROM doctors")
    records = cursor.fetchall()
    for record in records:
        doctor_list.insert("", END, values=record)

Label(doctor_frame, text="Doctor Name").grid(row=0, column=0)
Label(doctor_frame, text="Specialization").grid(row=1, column=0)
Label(doctor_frame, text="Contact").grid(row=2, column=0)

doctor_name_entry = Entry(doctor_frame)
specialization_entry = Entry(doctor_frame)
doctor_contact_entry = Entry(doctor_frame)

doctor_name_entry.grid(row=0, column=1)
specialization_entry.grid(row=1, column=1)
doctor_contact_entry.grid(row=2, column=1)

Button(doctor_frame, text="Add Doctor", command=add_doctor).grid(row=3, column=0)
Button(doctor_frame, text="View Doctors", command=view_doctors).grid(row=3, column=1)

doctor_list = ttk.Treeview(doctor_frame, columns=("ID", "Name", "Specialization", "Contact"), show='headings')
for col in ("ID", "Name", "Specialization", "Contact"):
    doctor_list.heading(col, text=col)
doctor_list.grid(row=4, column=0, columnspan=2)

# Appointment Management Frame
appointment_frame = Frame(notebook)
notebook.add(appointment_frame, text="Appointments")

def add_appointment():
    patient_id = patient_id_entry.get()
    doctor_id = doctor_id_entry.get()
    date = date_entry.get()
    time = time_entry.get()
    cursor.execute("INSERT INTO appointments (patient_id, doctor_id, date, time) VALUES (?, ?, ?, ?)",
                   (patient_id, doctor_id, date, time))
    conn.commit()
    messagebox.showinfo("Success", "Appointment added successfully")

def view_appointments():
    cursor.execute("SELECT * FROM appointments")
    records = cursor.fetchall()
    for record in records:
        appointment_list.insert("", END, values=record)

Label(appointment_frame, text="Patient ID").grid(row=0, column=0)
Label(appointment_frame, text="Doctor ID").grid(row=1, column=0)
Label(appointment_frame, text="Date").grid(row=2, column=0)
Label(appointment_frame, text="Time").grid(row=3, column=0)

patient_id_entry = Entry(appointment_frame)
doctor_id_entry = Entry(appointment_frame)
date_entry = Entry(appointment_frame)
time_entry = Entry(appointment_frame)

patient_id_entry.grid(row=0, column=1)
doctor_id_entry.grid(row=1, column=1)
date_entry.grid(row=2, column=1)
time_entry.grid(row=3, column=1)

Button(appointment_frame, text="Add Appointment", command=add_appointment).grid(row=4, column=0)
Button(appointment_frame, text="View Appointments", command=view_appointments).grid(row=4, column=1)

appointment_list = ttk.Treeview(appointment_frame, columns=("ID", "Patient ID", "Doctor ID", "Date", "Time"), show='headings')
for col in ("ID", "Patient ID", "Doctor ID", "Date", "Time"):
    appointment_list.heading(col, text=col)
appointment_list.grid(row=5, column=0, columnspan=2)

# Running the application
root.mainloop()

conn.close()
