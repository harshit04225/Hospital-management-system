# Hospital-management-system

#include <iostream>
#include <fstream>
using namespace std;

// ================= CLASSES =================
class Patient {
public:
    int id, age;
    string name, disease;
};

class Doctor {
public:
    int id;
    string name, specialization;
};

class Appointment {
public:
    int patientId, doctorId;
    string date;
};

// ================= VALIDATION =================
bool patientExists(int id) {
    ifstream file("patients.txt");
    Patient p;
    while (file >> p.id >> p.name >> p.age >> p.disease) {
        if (p.id == id) return true;
    }
    return false;
}

bool doctorExists(int id) {
    ifstream file("doctors.txt");
    Doctor d;
    while (file >> d.id >> d.name >> d.specialization) {
        if (d.id == id) return true;
    }
    return false;
}

// ================= LOOKUP (FINAL UPGRADE) =================
string getPatientName(int id) {
    ifstream file("patients.txt");
    Patient p;
    while (file >> p.id >> p.name >> p.age >> p.disease) {
        if (p.id == id) return p.name;
    }
    return "Unknown";
}

string getDoctorName(int id) {
    ifstream file("doctors.txt");
    Doctor d;
    while (file >> d.id >> d.name >> d.specialization) {
        if (d.id == id) return d.name;
    }
    return "Unknown";
}

string getDoctorSpecialization(int id) {
    ifstream file("doctors.txt");
    Doctor d;
    while (file >> d.id >> d.name >> d.specialization) {
        if (d.id == id) return d.specialization;
    }
    return "Unknown";
}

// ================= ADD =================
void addPatient() {
    Patient p;
    bool exists;

    do {
        cout << "Enter Patient ID: ";
        cin >> p.id;

        exists = patientExists(p.id);
        if (exists) cout << "ID already exists! Try again.\n";

    } while (exists);

    cout << "Enter Name: ";
    cin >> p.name;
    cout << "Enter Age: ";
    cin >> p.age;
    cout << "Enter Disease: ";
    cin >> p.disease;

    ofstream file("patients.txt", ios::app);
    file << p.id << " " << p.name << " " << p.age << " " << p.disease << endl;

    cout << "Patient Added!\n";
}

void addDoctor() {
    Doctor d;
    bool exists;

    do {
        cout << "Enter Doctor ID: ";
        cin >> d.id;

        exists = doctorExists(d.id);
        if (exists) cout << "ID already exists! Try again.\n";

    } while (exists);

    cout << "Enter Name: ";
    cin >> d.name;
    cout << "Enter Specialization: ";
    cin >> d.specialization;

    ofstream file("doctors.txt", ios::app);
    file << d.id << " " << d.name << " " << d.specialization << endl;

    cout << "Doctor Added!\n";
}

// ================= VIEW =================
void viewPatients() {
    ifstream file("patients.txt");
    Patient p;

    while (file >> p.id >> p.name >> p.age >> p.disease) {
        cout << "\nID: " << p.id
             << "\nName: " << p.name
             << "\nAge: " << p.age
             << "\nDisease: " << p.disease
             << "\n-------------------";
    }
}

void viewDoctors() {
    ifstream file("doctors.txt");
    Doctor d;

    while (file >> d.id >> d.name >> d.specialization) {
        cout << "\nID: " << d.id
             << "\nName: " << d.name
             << "\nSpecialization: " << d.specialization
             << "\n-------------------";
    }
}

// ================= DELETE =================
void deletePatient() {
    int id;
    cout << "Enter Patient ID to delete: ";
    cin >> id;

    ifstream file("patients.txt");
    ofstream temp("temp.txt");

    Patient p;
    bool found = false;

    while (file >> p.id >> p.name >> p.age >> p.disease) {
        if (p.id == id) {
            found = true;
            continue;
        }
        temp << p.id << " " << p.name << " " << p.age << " " << p.disease << endl;
    }

    file.close();
    temp.close();

    remove("patients.txt");
    rename("temp.txt", "patients.txt");

    if (found)
        cout << "Patient deleted!\n";
    else
        cout << "Patient not found!\n";
}

void deleteDoctor() {
    int id;
    cout << "Enter Doctor ID to delete: ";
    cin >> id;

    ifstream file("doctors.txt");
    ofstream temp("temp.txt");

    Doctor d;
    bool found = false;

    while (file >> d.id >> d.name >> d.specialization) {
        if (d.id == id) {
            found = true;
            continue;
        }
        temp << d.id << " " << d.name << " " << d.specialization << endl;
    }

    file.close();
    temp.close();

    remove("doctors.txt");
    rename("temp.txt", "doctors.txt");

    if (found)
        cout << "Doctor deleted!\n";
    else
        cout << "Doctor not found!\n";
}

void deleteAllPatients() {
    char confirm;
    cout << "Delete ALL patients? (y/n): ";
    cin >> confirm;

    if (confirm == 'y' || confirm == 'Y') {
        ofstream file("patients.txt", ios::trunc);
        file.close();
        cout << "All patients deleted!\n";
    } else {
        cout << "Cancelled.\n";
    }
}

// ================= APPOINTMENTS =================
void bookAppointment() {
    Appointment a;

    cout << "Enter Patient ID: ";
    cin >> a.patientId;
    if (!patientExists(a.patientId)) {
        cout << "Patient does not exist!\n";
        return;
    }

    cout << "Enter Doctor ID: ";
    cin >> a.doctorId;
    if (!doctorExists(a.doctorId)) {
        cout << "Doctor does not exist!\n";
        return;
    }

    cout << "Enter Date: ";
    cin >> a.date;

    ofstream file("appointments.txt", ios::app);
    file << a.patientId << " " << a.doctorId << " " << a.date << endl;

    cout << "Appointment booked!\n";
}

void viewAppointments() {
    ifstream file("appointments.txt");
    Appointment a;

    while (file >> a.patientId >> a.doctorId >> a.date) {
        cout << "\nPatient ID: " << a.patientId;
        cout << "\nPatient Name: " << getPatientName(a.patientId);

        cout << "\nDoctor ID: " << a.doctorId;
        cout << "\nDoctor Name: " << getDoctorName(a.doctorId);
        cout << "\nSpecialization: " << getDoctorSpecialization(a.doctorId);

        cout << "\nDate: " << a.date;
        cout << "\n-----------------------------";
    }
}

// ================= MAIN =================
int main() {
    int choice;

    do {
        cout << "\n===== Hospital Management System =====\n";
        cout << "1. Add Patient\n";
        cout << "2. View Patients\n";
        cout << "3. Add Doctor\n";
        cout << "4. View Doctors\n";
        cout << "5. Book Appointment\n";
        cout << "6. View Appointments\n";
        cout << "7. Delete Patient\n";
        cout << "8. Delete Doctor\n";
        cout << "9. Delete ALL Patients\n";
        cout << "10. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {
            case 1: addPatient(); break;
            case 2: viewPatients(); break;
            case 3: addDoctor(); break;
            case 4: viewDoctors(); break;
            case 5: bookAppointment(); break;
            case 6: viewAppointments(); break;
            case 7: deletePatient(); break;
            case 8: deleteDoctor(); break;
            case 9: deleteAllPatients(); break;
            case 10: cout << "Exiting...\n"; break;
            default: cout << "Invalid choice!\n";
        }

    } while (choice != 10);

    return0;

