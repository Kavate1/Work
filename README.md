#include <iostream>
#include <vector>
#include <string>
#include <fstream>
#include <algorithm>
#include <random>

using namespace std;

// Structure to represent a student
struct Student {
    string firstName;
    string lastName;
    char gender;
    int age;
    int group;
    vector<string> activities;
};

// Structure to represent a club/society
struct Club {
    string name;
    int capacity;
    int maleCount;
    int femaleCount;
};

// Structure to represent a sport
struct Sport {
    string name;
    int capacity;
    int maleCount;
    int femaleCount;
};

// Function to display the main menu
void displayMainMenu() {
    cout << "\n*** Co-curricular Activity Selection System ***\n";
    cout << "1. Add Student\n";
    cout << "2. View Students\n";
    cout << "3. View Clubs/Societies\n";
    cout << "4. View Sports\n";
    cout << "5. View Grouped Students\n";
    cout << "6. Save Data\n";
    cout << "7. Exit\n";
    cout << "Enter your choice: ";
}

// Function to add a new student
void addStudent(vector<Student>& students) {
    Student newStudent;
    cout << "\nEnter Student First Name: ";
    cin >> newStudent.firstName;
    cout << "Enter Student Last Name: ";
    cin >> newStudent.lastName;
    cout << "Enter Student Gender (M/F): ";
    cin >> newStudent.gender;
    cout << "Enter Student Age: ";
    cin >> newStudent.age;
    cout << "Enter Student Group (1-3): ";
    cin >> newStudent.group;
    students.push_back(newStudent);
    cout << "\nStudent added successfully!\n";
}

// Function to display all students
void viewStudents(const vector<Student>& students) {
    if (students.empty()) {
        cout << "\nNo students added yet.\n";
        return;
    }
    cout << "\nStudent List:\n";
    for (const auto& student : students) {
        cout << "Name: " << student.firstName << " " << student.lastName << endl;
        cout << "Gender: " << student.gender << endl;
        cout << "Age: " << student.age << endl;
        cout << "Group: " << student.group << endl;
        cout << "Activities: ";
        if (student.activities.empty()) {
            cout << "None\n";
        } else {
            for (const auto& activity : student.activities) {
                cout << activity << " ";
            }
            cout << endl;
        }
        cout << "---------------------\n";
    }
}

// Function to display all clubs/societies
void viewClubs(const vector<Club>& clubs) {
    if (clubs.empty()) {
        cout << "\nNo clubs/societies added yet.\n";
        return;
    }
    cout << "\nClub/Society List:\n";
    for (const auto& club : clubs) {
        cout << "Name: " << club.name << endl;
        cout << "Capacity: " << club.capacity << endl;
        cout << "Male Count: " << club.maleCount << endl;
        cout << "Female Count: " << club.femaleCount << endl;
        cout << "---------------------\n";
    }
}

// Function to display all sports
void viewSports(const vector<Sport>& sports) {
    if (sports.empty()) {
        cout << "\nNo sports added yet.\n";
        return;
    }
    cout << "\nSport List:\n";
    for (const auto& sport : sports) {
        cout << "Name: " << sport.name << endl;
        cout << "Capacity: " << sport.capacity << endl;
        cout << "Male Count: " << sport.maleCount << endl;
        cout << "Female Count: " << sport.femaleCount << endl;
        cout << "---------------------\n";
    }
}

// Function to view grouped students
void viewGroupedStudents(const vector<Student>& students) {
    if (students.empty()) {
        cout << "\nNo students added yet.\n";
        return;
    }
    cout << "\nGrouped Student List:\n";
    for (int i = 1; i <= 3; ++i) {
        cout << "\nGroup " << i << ":\n";
        for (const auto& student : students) {
            if (student.group == i) {
                cout << "Name: " << student.firstName << " " << student.lastName << endl;
                cout << "Activities: ";
                if (student.activities.empty()) {
                    cout << "None\n";
                } else {
                    for (const auto& activity : student.activities) {
                        cout << activity << " ";
                    }
                    cout << endl;
                }
                cout << "---------------------\n";
            }
        }
    }
}

// Function to save data to a CSV file
void saveData(const vector<Student>& students, const vector<Club>& clubs, const vector<Sport>& sports) {
    ofstream outputFile("data.csv");
    if (outputFile.is_open()) {
        // Write student data
        outputFile << "Student,FirstName,LastName,Gender,Age,Group,Activities\n";
        for (const auto& student : students) {
            outputFile << "Student," << student.firstName << "," << student.lastName << "," << student.gender << ","
                      << student.age << "," << student.group << ",";
            for (const auto& activity : student.activities) {
                outputFile << activity << " ";
            }
            outputFile << "\n";
        }

        // Write club/society data
        outputFile << "\nClub,Name,Capacity,MaleCount,FemaleCount\n";
        for (const auto& club : clubs) {
            outputFile << "Club," << club.name << "," << club.capacity << "," << club.maleCount << ","
                      << club.femaleCount << "\n";
        }

        // Write sport data
        outputFile << "\nSport,Name,Capacity,MaleCount,FemaleCount\n";
        for (const auto& sport : sports) {
            outputFile << "Sport," << sport.name << "," << sport.capacity << "," << sport.maleCount << ","
                      << sport.femaleCount << "\n";
        }
        outputFile.close();
        cout << "\nData saved successfully to data.csv!\n";
    } else {
        cout << "\nError opening file for saving data.\n";
    }
}

// Function to allocate activities to students
void allocateActivities(vector<Student>& students, vector<Club>& clubs, vector<Sport>& sports) {
    // Initialize random number generator
    random_device rd;
    mt19937 gen(rd());
    uniform_int_distribution<> distrib(0, 9);

    for (auto& student : students) {
        // Determine the number of activities based on the student's sport participation
        int maxActivities = student.activities.empty() ? 3 : 2;
        int minActivities = student.activities.empty() ? 1 : 1;

        // Allocate activities randomly until the minimum is reached
        while (student.activities.size() < minActivities) {
            // Randomly select a club/society or a sport
            if (distrib(gen) % 2 == 0) {
                // Allocate a club/society
                for (auto& club : clubs) {
                    if (club.capacity > 0 && club.maleCount < club.capacity / 2 && club.femaleCount < club.capacity / 2) {
                        if (student.activities.size() < maxActivities) {
                            student.activities.push_back(club.name);
                            if (student.gender == 'M') {
                                club.maleCount++;
                            } else {
                                club.femaleCount++;
                            }
                            club.capacity--;
                            break;
                        }
                    }
                }
            } else {
                // Allocate a sport
                for (auto& sport : sports) {
                    if (sport.capacity > 0 && sport.maleCount < sport.capacity * 0.75 && sport.femaleCount < sport.capacity * 0.75) {
                        if (student.activities.size() < maxActivities) {
                            student.activities.push_back(sport.name);
                            if (student.gender == 'M') {
                                sport.maleCount++;
                            } else {
                                sport.femaleCount++;
                            }
                            sport.capacity--;
                            break;
                        }
                    }
                }
            }
        }
    }
    cout << "\nActivities allocated successfully!\n";
}


int main() {
    // Initialize vectors for students, clubs/societies, and sports
    vector<Student> students;
    vector<Club> clubs;
    vector<Sport> sports;

    // Pre-saved students
    students.push_back({"Jane", "Doe", 'F', 18, 1, {}});
    students.push_back({"John", "Smith", 'M', 19, 2, {}});
    students.push_back({"Emily", "Brown", 'F', 18, 3, {}});
    students.push_back({"David", "Wilson", 'M', 19, 1, {}});
    students.push_back({"Sarah", "Jones", 'F', 18, 2, {}});
    students.push_back({"Michael", "Williams", 'M', 19, 3, {}});
    students.push_back({"Olivia", "Davis", 'F', 18, 1, {}});
    students.push_back({"James", "Miller", 'M', 19, 2, {}});
    students.push_back({"Sophia", "Moore", 'F', 18, 3, {}});
    students.push_back({"Ethan", "Taylor", 'M', 19, 1, {}});
    students.push_back({"Ava", "Anderson", 'F', 18, 2, {}});
    students.push_back({"Noah", "Thomas", 'M', 19, 3, {}});
    students.push_back({"Isabella", "Jackson", 'F', 18, 1, {}});
    students.push_back({"Liam", "White", 'M', 19, 2, {}});
    students.push_back({"Mia", "Harris", 'F', 18, 3, {}});
    students.push_back({"Logan", "Martin", 'M', 19, 1, {}});
    students.push_back({"Abigail", "Thompson", 'F', 18, 2, {}});
    students.push_back({"Mason", "Garcia", 'M', 19, 3, {}});
    students.push_back({"Elizabeth", "Martinez", 'F', 18, 1, {}});
    students.push_back({"Jacob", "Robinson", 'M', 19, 2, {}});

    // Initialize clubs/societies
    clubs.push_back({"Journalism Club", 60, 0, 0});
    clubs.push_back({"Red Cross Society", 60, 0, 0});
    clubs.push_back({"AISEC", 60, 0, 0});
    clubs.push_back({"Business Club", 60, 0, 0});
    clubs.push_back({"Computer Science Club", 60, 0, 0});

    // Initialize sports
    sports.push_back({"Rugby", 20, 0, 0});
    sports.push_back({"Athletics", 20, 0, 0});
    sports.push_back({"Swimming", 20, 0, 0});
    sports.push_back({"Soccer", 20, 0, 0});

    // Allocate activities to students
    allocateActivities(students, clubs, sports);

    // Main program loop
    int choice;
    do {
        displayMainMenu();
        cin >> choice;
        switch (choice) {
            case 1:
                addStudent(students);
                break;
            case 2:
                viewStudents(students);
                break;
            case 3:
                viewClubs(clubs);
                break;
            case 4:
                viewSports(sports);
                break;
            case 5:
                viewGroupedStudents(students);
                break;
            case 6:
                saveData(students, clubs, sports);
                break;
            case 7:
                cout << "\nExiting program...\n";
                break;
            default:
                cout << "\nInvalid choice. Please try again.\n";
        }
    } while (choice != 7);

    return 0;
}