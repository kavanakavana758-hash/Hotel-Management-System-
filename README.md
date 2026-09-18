# Hotel-Management-System-
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <iomanip>
#include <limits>
using namespace std;

// =========================
// Customer Class
// =========================
class Customer {
private:
    string name;
    string phone;

public:
    Customer() {}

    Customer(string n, string p) {
        name = n;
        phone = p;
    }

    string getName() const {
        return name;
    }

    string getPhone() const {
        return phone;
    }
};

// =========================
// Room Class
// =========================
class Room {
private:
    int roomNumber;
    string type;
    double price;
    bool booked;
    Customer customer;

public:
    Room() {
        roomNumber = 0;
        type = "";
        price = 0;
        booked = false;
    }

    Room(int number, string roomType, double roomPrice) {
        roomNumber = number;
        type = roomType;
        price = roomPrice;
        booked = false;
    }

    int getRoomNumber() const {
        return roomNumber;
    }

    string getType() const {
        return type;
    }

    double getPrice() const {
        return price;
    }

    bool isBooked() const {
        return booked;
    }

    void bookRoom(Customer c) {
        booked = true;
        customer = c;
    }

    void checkout() {
        booked = false;
        customer = Customer();
    }

    Customer getCustomer() const {
        return customer;
    }

    // Save room information to file
    void saveToFile(ofstream &file) const {
        file << roomNumber << "|"
             << type << "|"
             << price << "|"
             << booked << "|"
             << customer.getName() << "|"
             << customer.getPhone() << endl;
    }

    // Display room details
    void display() const {
        cout << left
             << setw(10) << roomNumber
             << setw(15) << type
             << setw(12) << price;

        if (booked) {
            cout << setw(15) << "Booked"
                 << customer.getName() << endl;
        } else {
            cout << setw(15) << "Available"
                 << "-" << endl;
        }
    }
};

// =========================
// Hotel Management Class
// =========================
class Hotel {
private:
    vector<Room> rooms;
    const string filename = "hotel_records.txt";

public:

    // Create rooms
    void initializeRooms() {
        rooms.push_back(Room(101, "Single", 1000));
        rooms.push_back(Room(102, "Single", 1000));
        rooms.push_back(Room(103, "Single", 1000));

        rooms.push_back(Room(201, "Double", 1800));
        rooms.push_back(Room(202, "Double", 1800));
        rooms.push_back(Room(203, "Double", 1800));

        rooms.push_back(Room(301, "Deluxe", 2500));
        rooms.push_back(Room(302, "Deluxe", 2500));
        rooms.push_back(Room(303, "Deluxe", 2500));

        rooms.push_back(Room(401, "Suite", 4000));
        rooms.push_back(Room(402, "Suite", 4000));
    }

    // Find room by room number
    int findRoom(int roomNumber) {
        for (int i = 0; i < rooms.size(); i++) {
            if (rooms[i].getRoomNumber() == roomNumber) {
                return i;
            }
        }
        return -1;
    }

    // Display all rooms
    void displayRooms() {
        cout << "\n============================================================\n";
        cout << "                     ROOM DETAILS\n";
        cout << "============================================================\n";

        cout << left
             << setw(10) << "Room No."
             << setw(15) << "Type"
             << setw(12) << "Price"
             << setw(15) << "Status"
             << "Customer" << endl;

        cout << "------------------------------------------------------------\n";

        for (const Room &room : rooms) {
            room.display();
        }

        cout << "============================================================\n";
    }

    // Book a room
    void bookRoom() {
        int roomNumber;
        string name, phone;

        cout << "\nEnter room number to book: ";
        cin >> roomNumber;

        int index = findRoom(roomNumber);

        if (index == -1) {
            cout << "Room not found!\n";
            return;
        }

        if (rooms[index].isBooked()) {
            cout << "ERROR: Room " << roomNumber
                 << " is already booked!\n";
            cout << "Double booking is not allowed.\n";
            return;
        }

        cin.ignore(numeric_limits<streamsize>::max(), '\n');

        cout << "Enter customer name: ";
        getline(cin, name);

        cout << "Enter customer phone number: ";
        getline(cin, phone);

        if (name.empty() || phone.empty()) {
            cout << "Invalid customer details!\n";
            return;
        }

        Customer customer(name, phone);

        rooms[index].bookRoom(customer);

        saveData();

        cout << "\nRoom booked successfully!\n";
        cout << "Room Number : " << roomNumber << endl;
        cout << "Customer    : " << name << endl;
        cout << "Phone       : " << phone << endl;
        cout << "Price       : Rs. " << rooms[index].getPrice() << endl;
    }

    // Checkout
    void checkoutRoom() {
        int roomNumber;

        cout << "\nEnter room number for checkout: ";
        cin >> roomNumber;

        int index = findRoom(roomNumber);

        if (index == -1) {
            cout << "Room not found!\n";
            return;
        }

        if (!rooms[index].isBooked()) {
            cout << "Room " << roomNumber
                 << " is not currently booked.\n";
            return;
        }

        cout << "\nCustomer: "
             << rooms[index].getCustomer().getName() << endl;

        cout << "Room price: Rs. "
             << rooms[index].getPrice() << endl;

        rooms[index].checkout();

        saveData();

        cout << "Checkout completed successfully!\n";
        cout << "Room " << roomNumber
             << " is now available.\n";
    }

    // Search room
    void searchRoom() {
        int roomNumber;

        cout << "\nEnter room number to search: ";
        cin >> roomNumber;

        int index = findRoom(roomNumber);

        if (index == -1) {
            cout << "Room not found!\n";
            return;
        }

        cout << "\nRoom found:\n";
        cout << "Room Number : "
             << rooms[index].getRoomNumber() << endl;

        cout << "Room Type   : "
             << rooms[index].getType() << endl;

        cout << "Price       : Rs. "
             << rooms[index].getPrice() << endl;

        if (rooms[index].isBooked()) {
            cout << "Status      : Booked\n";
            cout << "Customer    : "
                 << rooms[index].getCustomer().getName() << endl;
            cout << "Phone       : "
                 << rooms[index].getCustomer().getPhone() << endl;
        } else {
            cout << "Status      : Available\n";
        }
    }

    // Search customer
    void searchCustomer() {
        string name;
        bool found = false;

        cin.ignore(numeric_limits<streamsize>::max(), '\n');

        cout << "\nEnter customer name: ";
        getline(cin, name);

        for (const Room &room : rooms) {
            if (room.isBooked() &&
                room.getCustomer().getName() == name) {

                cout << "\nCustomer found!\n";
                cout << "Name        : "
                     << room.getCustomer().getName() << endl;

                cout << "Phone       : "
                     << room.getCustomer().getPhone() << endl;

                cout << "Room Number : "
                     << room.getRoomNumber() << endl;

                cout << "Room Type   : "
                     << room.getType() << endl;

                found = true;
            }
        }

        if (!found) {
            cout << "Customer not found!\n";
        }
    }

    // Save data to file
    void saveData() {
        ofstream file(filename);

        if (!file) {
            cout << "Error: Unable to save data!\n";
            return;
        }

        for (const Room &room : rooms) {
            room.saveToFile(file);
        }

        file.close();
    }

    // Load data from file
    void loadData() {
        ifstream file(filename);

        if (!file) {
            return;
        }

        string roomNumberStr;
        string type;
        string priceStr;
        string bookedStr;
        string name;
        string phone;

        while (getline(file, roomNumberStr, '|') &&
               getline(file, type, '|') &&
               getline(file, priceStr, '|') &&
               getline(file, bookedStr, '|') &&
               getline(file, name, '|') &&
               getline(file, phone)) {

            int roomNumber = stoi(roomNumberStr);
            double price = stod(priceStr);
            bool booked = stoi(bookedStr);

            int index = findRoom(roomNumber);

            if (index != -1) {
                if (booked) {
                    Customer customer(name, phone);
                    rooms[index].bookRoom(customer);
                }
            }
        }

        file.close();
    }

    // Main menu
    void menu() {
        int choice;

        do {
            cout << "\n=========================================\n";
            cout << "        HOTEL MANAGEMENT SYSTEM\n";
            cout << "=========================================\n";
            cout << "1. Display All Rooms\n";
            cout << "2. Book a Room\n";
            cout << "3. Checkout\n";
            cout << "4. Search Room\n";
            cout << "5. Search Customer\n";
            cout << "6. Exit\n";
            cout << "=========================================\n";
            cout << "Enter your choice: ";

            if (!(cin >> choice)) {
                cout << "Invalid input! Please enter a number.\n";

                cin.clear();
                cin.ignore(
                    numeric_limits<streamsize>::max(),
                    '\n'
                );

                continue;
            }

            switch (choice) {

                case 1:
                    displayRooms();
                    break;

                case 2:
                    bookRoom();
                    break;

                case 3:
                    checkoutRoom();
                    break;

                case 4:
                    searchRoom();
                    break;

                case 5:
                    searchCustomer();
                    break;

                case 6:
                    cout << "\nThank you for using "
                         << "Hotel Management System!\n";
                    break;

                default:
                    cout << "Invalid choice! "
                         << "Please select 1-6.\n";
            }

        } while (choice != 6);
    }
};

// =========================
// Main Function
// =========================
int main() {

    Hotel hotel;

    // Create room list
    hotel.initializeRooms();

    // Load previously saved bookings
    hotel.loadData();

    // Start hotel management system
    hotel.menu();

    return 0;
}