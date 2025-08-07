#include <iostream>
#include <filesystem>
#include <regex>
#include <string>

using namespace std;
using namespace std::filesystem;

void listAllFiles(const path& dir) {
    cout << "\n--- All Files ---\n";
    for (const auto& entry : directory_iterator(dir)) {
        if (is_regular_file(entry)) {
            cout << " - " << entry.path().filename().string() << "\n";
        }
    }
}

void listFilesByExtension(const path& dir) {
    string ext;
    cout << "Enter extension (e.g., .txt): ";
    cin >> ext;

    cout << "\n--- Files with extension '" << ext << "' ---\n";
    for (const auto& entry : directory_iterator(dir)) {
        if (is_regular_file(entry) && entry.path().extension() == ext) {
            cout << " - " << entry.path().filename().string() << "\n";
        }
    }
}
void listFilesByPattern(const path& dir) {
    string pattern;
    cout << "Enter pattern (e.g., moha*.*): ";
    cin >> pattern;

    string regexPattern = regex_replace(pattern, regex(R"(\.)"), R"(\.)");
    regexPattern = regex_replace(regexPattern, regex(R"(\*)"), ".*");
    regexPattern = regex_replace(regexPattern, regex(R"(\?)"), ".");
    regex fileRegex(regexPattern, regex_constants::icase);

    cout << "\n--- Files matching pattern '" << pattern << "' ---\n";
    for (const auto& entry : directory_iterator(dir)) {
        string filename = entry.path().filename().string();
        if (is_regular_file(entry) && regex_match(filename, fileRegex)) {
            cout << " - " << filename << "\n";
        }
    }
}

void listFilesMenu() {
    int option;
    path currentDir = current_path();

    cout << "\n[List Files Submenu]\n";
    cout << "[1] List All Files\n";
    cout << "[2] List Files by Extension\n";
    cout << "[3] List Files by Pattern\n";
    cout << "Enter choice: ";
    cin >> option;

    switch (option) {
        case 1: listAllFiles(currentDir); break;
        case 2: listFilesByExtension(currentDir); break;
        case 3: listFilesByPattern(currentDir); break;
        default: cout << "Invalid option.\n";
    }
}

void createDirectory() {
    string dirName;
    cout << "Enter new directory name: ";
    cin >> dirName;

    if (exists(dirName)) {
        cout << "Error: Directory already exists.\n";
    } else {
        try {
            create_directory(dirName);
            cout << "Directory created successfully.\n";
        } catch (...) {
            cout << "Error: Failed to create directory.\n";
        }
    }
}

void changeDirectory() {
    string newPath;
    cout << "Enter path to change directory: ";
    cin >> newPath;

    if (exists(newPath) && is_directory(newPath)) {
        current_path(newPath);
        cout << "Current directory changed to: " << current_path() << "\n";
    } else {
        cout << "Error: Invalid directory path.\n";
    }
}

void mainMenu() {
    int choice;
    do {
        cout << "\n======= Directory Management System =======\n";
        cout << "Current Directory: " << current_path() << "\n";
        cout << "[1] List Files\n";
        cout << "[2] Create Directory\n";
        cout << "[3] Change Directory\n";
        cout << "[4] Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1: listFilesMenu(); break;
            case 2: createDirectory(); break;
            case 3: changeDirectory(); break;
            case 4: cout << "Exiting program...\n"; break;
            default: cout << "Invalid choice. Try again.\n";
        }
    } while (choice != 4);
}

int main() {
    mainMenu();
    return 0;
}

