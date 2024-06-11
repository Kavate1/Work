#include <iostream>
#include <vector>
#include <fstream>
#include <string>
#include <sstream>
#include <map>

using namespace std;

struct Student {
    string firstname;
    string surname;
    string gender;
    int age;
    int group;
    vector<string> activities;
};

struct Activity {
    string name;
    int max_capacity;
    int current_capacity;
    map<string, int> gender_count;
};

vector<Student> students;
map<string, Activity> sports;
map<string, Activity> clubs;

void initialize_activities() {
    sports["Rugby"] = {"Rugby", 20, 0, {{"Male", 0}, {"Female", 0}}};
    sports["Athletics"] = {"Athletics", 20, 0, {{"Male", 0}, {"Female", 0}}};
    sports["Swimming"] = {"Swimming", 20, 0, {{"Male", 0}, {"Female", 0}}};
    sports["Soccer"] = {"Soccer", 20, 0, {{"Male", 0}, {"Female", 0}}};

    clubs["Journalism Club"] = {"Journalism Club", 60, 0, {{"Male", 0}, {"Female", 0}}};
    clubs["Red Cross Society"] = {"Red Cross Society", 60, 0, {{"Male", 0}, {"Female", 0}}};
    clubs["AISEC"] = {"AISEC", 60, 0, {{"Male", 0}, {"Female", 0}}};
    clubs["Business Club"] = {"Business Club", 60, 0, {{"Male", 0}, {"Female", 0}}};
    clubs["Computer Science Club"] = {"Computer Science Club", 60, 0, {{"Male", 0}, {"Female", 0}}};

    // List of predefined students and their assignments
    vector<Student> predefined_students = {
        {"John", "Doe", "Male", 18, 1, {"Rugby", "AISEC"}},
        {"Jane", "Smith", "Female", 19, 1, {"Swimming", "Business Club"}},
        {"Alice", "Johnson", "Female", 18, 2, {"Soccer", "Red Cross Society"}},
        {"Bob", "Brown", "Male", 20, 2, {"Athletics", "Journalism Club"}},
        {"Charlie", "Davis", "Male", 19, 3, {"Computer Science Club", "AISEC", "Business Club"}},
        {"Diana", "Miller", "Female", 18, 3, {"Journalism Club", "Red Cross Society", "AISEC"}}
    };

    for (auto& s : predefined_students) {
        for (const auto& activity : s.activities) {
            if (sports.find(activity) != sports.end()) {
                sports[activity].current_capacity++;
                sports[activity].gender_count[s.gender]++;
            } else if (clubs.find(activity) != clubs.end()) {
                clubs[activity].current_capacity++;
                clubs[activity].gender_count[s.gender]++;
            }
        }
        students.push_back(s);
    }
}

bool add_student() {
    Student s;
    cout << "Enter Firstname: ";
    cin >> s.firstname;
    cout << "Enter Surname: ";
    cin >> s.surname;
    cout << "Enter Gender (Male/Female): ";
    cin >> s.gender;
    cout << "Enter Age: ";
    cin >> s.age;
    cout << "Enter Group (1/2/3): ";
    cin >> s.group;

    if (s.group < 1 || s.group > 3) {
        cout << "Invalid group number." << endl;
        return false;
    }

    cout << "Choose activities (sporting activities first, then club/societies). Type 'done' when finished:\n";
    string activity;
    int sport_count = 0;
    int club_count = 0;

    while (true) {
        cin >> activity;
        if (activity == "done") break;

        if (sports.find(activity) != sports.end()) {
            if (sport_count >= 1) {
                cout << "You can only choose one sporting activity." << endl;
                continue;
            }
            if (sports[activity].current_capacity >= sports[activity].max_capacity) {
                cout << "This sport is full." << endl;
                continue;
            }
            sports[activity].current_capacity++;
            sports[activity].gender_count[s.gender]++;
            sport_count++;
            s.activities.push_back(activity);
        } else if (clubs.find(activity) != clubs.end()) {
            if (sport_count == 1 && club_count >= 2) {
                cout << "You can only choose two clubs if you are participating in a sport." << endl;
                continue;
            } else if (sport_count == 0 && club_count >= 3) {
                cout << "You can only choose up to three clubs." << endl;
                continue;
            }
            if (clubs[activity].current_capacity >= clubs[activity].max_capacity) {
                cout << "This club is full." << endl;
                continue;
            }
            clubs[activity].current_capacity++;
            clubs[activity].gender_count[s.gender]++;
            club_count++;
            s.activities.push_back(activity);
        } else {
            cout << "Invalid activity. Try again." << endl;
        }
    }

    if (s.activities.empty()) {
        cout << "You must choose at least one activity." << endl;
        return false;
    }

    students.push_back(s);
    return true;
}

void view_students() {
    for (const auto& s : students) {
        cout << "Name: " << s.firstname << " " << s.surname << ", Gender: " << s.gender
             << ", Age: " << s.age << ", Group: " << s.group << ", Activities: ";
        for (const auto& act : s.activities) {
            cout << act << " ";
        }
        cout << endl;
    }
}

void view_clubs() {
    for (const auto& [name, club] : clubs) {
        cout << "Club: " << name << ", Capacity: " << club.current_capacity << "/" << club.max_capacity << endl;
    }
}

void view_sports() {
    for (const auto& [name, sport] : sports) {
        cout << "Sport: " << name << ", Capacity: " << sport.current_capacity << "/" << sport.max_capacity << endl;
    }
}

void save_to_file(const string& filename) {
    ofstream file(filename);
    if (file.is_open()) {
        for (const auto& s : students) {
            file << s.firstname << "," << s.surname << "," << s.gender << "," << s.age << "," << s.group << ",";
            for (const auto& act : s.activities) {
                file << act << " ";
            }
            file << endl;
        }
        file.close();
        cout << "Data saved to " << filename << endl;
    } else {
        cout << "Unable to open file." << endl;
    }
}

void menu() {
    int choice;
    initialize_activities();

    do {
        cout << "1. Add Student\n2. View Students\n3. View Clubs/ Societies\n4. View Sports\n5. Save All Files\n6. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1:
                if (add_student()) {
                    cout << "Student added successfully." << endl;
                } else {
                    cout << "Failed to add student." << endl;
                }
                break;
            case 2:
                view_students();
                break;
            case 3:
                view_clubs();
                break;
            case 4:
                view_sports();
                break;
            case 5:
                save_to_file("students.csv");
                break;
            case 6:
                cout << "Exiting..." << endl;
                break;
            default:
                cout << "Invalid choice. Try again." << endl;
                break;
        }
    } while (choice != 6);
}

int main() {
    menu();
    return 0;
}

