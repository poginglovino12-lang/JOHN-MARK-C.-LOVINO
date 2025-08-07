#include <iostream>
#include <filesystem>
#include <string>

#ifdef _WIN32
#include <direct.h>
#endif

namespace fs = std::filesystem;

int getChoice(int min, int max) {
    int choice;
    while (true) {
        std::cout << "Enter your choice: ";
        std::cin >> choice;

        if (std::cin.fail() || choice < min || choice > max) {
            std::cin.clear();
            std::cin.ignore(10000, '\n');
            std::cout << "Invalid input. Please enter a number between " << min << " and " << max << ".\n";
        } else {
            std::cin.ignore(10000, '\n');
            return choice;
        }
    }
}

void listAllFiles() {
    for (const auto& item : fs::directory_iterator(fs::current_path())) {
        if (fs::is_regular_file(item.path())) {
            std::cout << item.path().filename() << "\n";
        }
    }
}

void listFilesByExtension() {
    std::string ext;
    std::cout << "Enter file extension (e.g., .cpp): ";
    std::cin >> ext;

    for (const auto& item : fs::directory_iterator(fs::current_path())) {
        if (item.path().extension() == ext) {
            std::cout << item.path().filename() << "\n";
        }
    }
}

void listFilesMenu() {
    std::cout << "[1] List All Files\n";
    std::cout << "[2] List Files by Extensions\n";
    std::cout << "[3] List Files by Pattern\n";

    int option = getChoice(1, 3);

    switch (option) {
        case 1:
            listAllFiles();
            break;
        case 2:
            listFilesByExtension();
            break;
        case 3:
            std::cout << "Pattern matching not supported in this version.\n";
            break;
    }
}

void createDir() {
    std::string folder;
    std::cout << "Enter directory name to create: ";
    std::cin >> folder;

    if (!fs::exists(folder)) {
        fs::create_directory(folder);
        std::cout << "Directory \"" << folder << "\" created successfully.\n";
    } else {
        std::cout << "Directory \"" << folder << "\" already exists.\n";
    }
}

void changeDir() {
    std::cout << "[1] Move to Parent Directory\n";
    std::cout << "[2] Move to Root Directory\n";
    std::cout << "[3] Enter Custom Path (e.g., \"C:\\\\Users\\\\Me\")\n";

    int opt = getChoice(1, 3);
    std::string newPath;

    switch (opt) {
        case 1:
            fs::current_path(fs::current_path().parent_path());
            std::cout << "Moved to parent directory: " << fs::current_path() << "\n";
            break;
        case 2:
            fs::current_path(fs::current_path().root_path());
            std::cout << "Moved to root directory: " << fs::current_path() << "\n";
            break;
        case 3:
            std::cout << "Enter directory path to move to: ";
            std::getline(std::cin >> std::ws, newPath);
            if (fs::exists(newPath) && fs::is_directory(newPath)) {
                fs::current_path(newPath);
                std::cout << "Current Directory changed to: " << fs::current_path() << "\n";
            } else {
                std::cout << "Error: Directory \"Invalid path\" not found!\n";
            }
            break;
    }
}

void showMainMenu() {
    int selection;
    do {
        std::cout << "DIRECTORY MANAGEMENT SYSTEM\n";
        std::cout << "[1] List Files\n";
        std::cout << "[2] Create Directory\n";
        std::cout << "[3] Change Directory\n";
        std::cout << "[4] Exit List Files\n";

        selection = getChoice(1, 4);

        switch (selection) {
            case 1: listFilesMenu(); break;
            case 2: createDir(); break;
            case 3: changeDir(); break;
            case 4: std::cout << "Exiting program...\n"; break;
        }
    } while (selection != 4);
}

int main() {
    showMainMenu();
    return 0;
}
