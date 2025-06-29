# Create new file and write "Hello, World!"

## Program Code

c
// Create new file and write "Hello, World!"

#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("newfile.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return 1;
    }

    write(fd, "Hello, World!", 13);  // 13 characters
    close(fd);
    return 0;
}




# Open file and display contents

## Program Code

c
//Open file and display contents

#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    char buf;
    int fd = open("newfile.txt", O_RDONLY);
    if (fd == -1) {
        perror("open");
        return 1;
    }

    while (read(fd, &buf, 1) > 0) {
        write(1, &buf, 1);  // write to STDOUT
    }

    close(fd);
    return 0;
}




# Create a directory "Test"

## Program Code

c
// Create a directory "Test"

#include <sys/stat.h>
#include <stdio.h>

int main() {
    if (mkdir("Test", 0777) == -1) {
        perror("mkdir");
        return 1;
    }

    return 0;
}




# Check if a file exists

## Program Code

c
//Check if a file exists

#include <unistd.h>
#include <stdio.h>

int main() {
    if (access("newfile.txt", F_OK) == 0) {
        write(1, "File exists\n", 12);
    } else {
        write(1, "File does not exist\n", 21);
    }

    return 0;
}




# Rename a file

##  Program Code

c
//Rename a file

#include <stdio.h>

int main() {
    if (rename("newfile.txt", "file.txt") == -1) {
        perror("rename");
        return 1;
    }

    return 0;
}




# Delete a file

##  Program Code

c
//Delete a file

#include <unistd.h>
#include <stdio.h>

int main() {
    if (unlink("txt.txt") == -1) {
        perror("unlink");
        return 1;
    }

    return 0;
}




# Copy contents from one file to another

##  Program Code

c
// Copy contents from one file to another

#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int src = open("file.txt", O_RDONLY);
    if (src == -1) {
        perror("open source");
        return 1;
    }

    int dest = open("copy.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (dest == -1) {
        perror("open dest");
        return 1;
    }

    char buf[1024];
    ssize_t n;
    while ((n = read(src, buf, sizeof(buf))) > 0) {
        write(dest, buf, n);
    }

    close(src);
    close(dest);
    return 0;
}




# Move file to another directory

##  Program Code

c
// Move file to another directory

#include <stdio.h>

int main() {
    if (rename("copy.txt", "Test/copy.txt") == -1) {
        perror("rename");
        return 1;
    }

    return 0;
}




# List all files in current directory

##  Program Code

c
// List all files in current directory

#include <dirent.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>

int main() {
    DIR *dir = opendir(".");
    if (!dir) {
        perror("opendir");
        return 1;
    }

    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        write(1, entry->d_name, strlen(entry->d_name));
        write(1, "\n", 1);
    }

    closedir(dir);
    return 0;
}




# Get size of file.txt

##  Program Code

c
// Get size of file.txt

#include <sys/stat.h>
#include <stdio.h>

int main() {
    struct stat st;
    if (stat("file.txt", &st) == -1) {
        perror("stat");
        return 1;
    }

    printf("Size: %ld bytes\n", st.st_size);
    return 0;
}




# Check if a directory named "Test" exists

##  Program Code

c
// Check if a directory named "Test" exists

#include <stdio.h>
#include <sys/stat.h>

int main() {
    struct stat st;
    if (stat("Test", &st) == -1) {
        perror("stat");
        return 1;
    }

    if (S_ISDIR(st.st_mode)) {
        printf("'Test' is a directory.\n");
    } else {
        printf("'Test' exists but is not a directory.\n");
    }

    return 0;
}




# Create "Backup" directory in the parent directory

##  Program Code

c
// Create "Backup" directory in the parent directory

#include <stdio.h>
#include <sys/stat.h>

int main() {
    if (mkdir("../Backup", 0777) == -1) {
        perror("mkdir");
        return 1;
    }

    printf("Directory '../Backup' created.\n");
    return 0;
}




# Recursively list all files/directories in a given directory

##  Program Code

c
// Recursively list all files/directories in a given directory

#include <stdio.h>
#include <dirent.h>
#include <string.h>
#include <sys/stat.h>
#include <unistd.h>

void list_recursive(const char *path) {
    DIR *dir = opendir(path);
    if (!dir) {
        perror("opendir");
        return;
    }

    struct dirent *entry;
    char fullpath[1024];

    while ((entry = readdir(dir)) != NULL) {
        // Skip "." and ".."
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0)
            continue;

        snprintf(fullpath, sizeof(fullpath), "%s/%s", path, entry->d_name);
        printf("%s\n", fullpath);

        struct stat st;
        if (stat(fullpath, &st) == 0 && S_ISDIR(st.st_mode)) {
            list_recursive(fullpath);  // Recursive call for subdirectories
        }
    }

    closedir(dir);
}

int main() {
    list_recursive(".");
    return 0;
}




# Delete all files in directory "Temp"

##  Program Code

c
// Delete all files in directory "Temp"

#include <stdio.h>
#include <dirent.h>
#include <unistd.h>
#include <string.h>
#include <sys/stat.h>

int main() {
    DIR *dir = opendir("Test");
    if (!dir) {
        perror("opendir");
        return 1;
    }

    struct dirent *entry;
    char filepath[512];

    while ((entry = readdir(dir)) != NULL) {
        if (entry->d_type == DT_REG) {  // Regular file
            snprintf(filepath, sizeof(filepath), "Temp/%s", entry->d_name);
            if (unlink(filepath) == -1) {
                perror("unlink");
            } else {
                printf("Deleted: %s\n", filepath);
            }
        }
    }

    closedir(dir);
    return 0;
}




# Count the number of lines in a file named "file.txt"

##  Program Code

c
//  Count the number of lines in a file named "file.txt"

#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("file.txt", O_RDONLY);
    if (fd == -1) {
        perror("open");
        return 1;
    }

    char c;
    int count = 0;
    int seen_any = 0;
    while (read(fd, &c, 1) > 0) {
        seen_any = 1;
        if (c == '\n') count++;
    }

    close(fd);
    if (seen_any && c != '\n') count++;  // count last line if not ending in \n

    printf("Line count: %d\n", count);
    return 0;
}




# C program to append "Goodbye!" to the end of an existing file named "file.txt"


##  Program Code

c
//C program to append "Goodbye!" to the end of an existing file named "file.txt"

#include <fcntl.h>
#include <unistd.h>
#include <string.h>

int main() {
    int fd = open("file.txt", O_WRONLY | O_APPEND);
    if (fd < 0) return 1;
    write(fd, "Goodbye!\n", strlen("Goodbye!\n"));
    close(fd);
    return 0;
}




# C program to change the permissions of a file named "file.txt" to read-only.


##  Program Code

c
//C program to change the permissions of a file named "file.txt" to read-only.

#include <sys/stat.h>

int main() {
    chmod("file.txt", 0444);
    return 0;
}




# C program to change the ownership of a file named "file.txt" to the user "Joycy"

##  Program Code

c
// C program to change the ownership of a file named "file.txt" to the user "Joycy"

#include <stdio.h>
#include <unistd.h>
#include <pwd.h>
#include <sys/types.h>

int main() {
    const char *filename = "file.txt";
    const char *new_owner = "Joycy";

    // Get UID of user1
    struct passwd *pwd = getpwnam(new_owner);
    if (pwd == NULL) {
        perror("getpwnam failed");
        return 1;
    }

    uid_t new_uid = pwd->pw_uid;
    gid_t new_gid = pwd->pw_gid;

    // Change ownership
    if (chown(filename, new_uid, new_gid) != 0) {
        perror("chown failed");
        return 1;
    }

    printf("Ownership of %s changed to %s\n", filename, new_owner);
    return 0;
}




# C program to get the last modified timestamp of a file named "file.txt


##  Program Code

c
// C program to get the last modified timestamp of a file named "file.txt

#include <sys/stat.h>
#include <stdio.h>
#include <time.h>

int main() {
    struct stat st;
    stat("file.txt", &st);
    printf("Last modified: %s", ctime(&st.st_mtime));
    return 0;
}




#  create a temporary file and write some data to it

##  Program Code

c
// a C program to create a temporary file and write some data to it

#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>

int main() {
    char tmpname[] = "/home/joycy/Joycy/linux_pro/file_op/tmpfileXXXXXX";
    int fd = mkstemp(tmpname);
    if (fd == -1) {
        perror("mkstemp failed");
        return 1;
    }
    write(fd, "Temporary data", 14);
    close(fd);
    printf("Temp file created: %s\n", tmpname);
    return 0;
}




# check if a given path refers to a file or a directory

##  Program Code

c
//C program to check if a given path refers to a file or a directory

#include <sys/stat.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (argc < 2) {
        printf("Usage: %s <path>\n", argv[0]);
        return 1;
    }

    struct stat st;
    if (stat(argv[1], &st) != 0) {
        perror("stat failed");
        return 1;
    }

    if (S_ISREG(st.st_mode)) printf("Regular file\n");
    else if (S_ISDIR(st.st_mode)) printf("Directory\n");
    else printf("Other file type\n");

    return 0;
}




# create a hard link named "hardlink.txt" to a file named "source.txt"


##  Program Code

c
// a C program to create a hard link named "hardlink.txt" to a file named "source.txt"

#include <stdio.h>
#include <unistd.h>

int main() {
    const char *existing_file = "file.txt";   // your source file
    const char *hard_link = "data.txt";       // your hard link name

    if (link(existing_file, hard_link) == -1) {
        perror("link");
        return 1;
    }

    printf("Hard link created successfully: %s → %s\n", hard_link, existing_file);
    return 0;
}




#  read and display the contents of a CSV file named "data.csv"


##  Program Code

c
/*program to read and display the contents of a CSV file named
"data.csv"*/

/*
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("data.csv", O_RDONLY);
    char buffer[1024];
    int n;
    while ((n = read(fd, buffer, sizeof(buffer))) > 0) {
        write(STDOUT_FILENO, buffer, n);
    }
    close(fd);
    return 0;
}
*/

#include <stdio.h>
#include <stdlib.h>

int main() {
    FILE *file = fopen("data.csv", "r");
    if (file == NULL) {
        perror("fopen");
        return 1;
    }

    char line[256];
    while (fgets(line, sizeof(line), file)) {
        printf("%s", line);
    }

    fclose(file);
    return 0;
}




# get the absolute path of the current working directory


##  Program Code

c
// C program to get the absolute path of the current working directory

#include <stdio.h>
#include <unistd.h>

int main() {
    char path[1024];
    if (getcwd(path, sizeof(path)) != NULL) {
        printf("Current working directory: %s\n", path);
    } else {
        perror("getcwd");
        return 1;
    }
    return 0;
}




# get the size of a directory named "file_op"


##  Program Code

c
// C program to get the size of a directory named "file_op"

/*
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <dirent.h>
#include <sys/stat.h>

long get_directory_size(const char *path) {
    long total_size = 0;
    struct dirent *entry;
    DIR *dir = opendir(path);

    if (dir == NULL) {
        perror("opendir");
        return -1;
    }

    char filepath[1024];
    struct stat st;

    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0)
            continue;

        snprintf(filepath, sizeof(filepath), "%s/%s", path, entry->d_name);
        if (stat(filepath, &st) == 0) {
            if (S_ISDIR(st.st_mode)) {
                total_size += get_directory_size(filepath); // recurse
            } else {
                total_size += st.st_size;
            }
        }
    }

    closedir(dir);
    return total_size;
}

int main() {
    const char *dir = "Test";
    long size = get_directory_size(dir);
    if (size >= 0) {
        printf("Total size of '%s': %ld bytes\n", dir, size);
    }
    return 0;
}
*/

#include <dirent.h>
#include <sys/stat.h>
#include <string.h>
#include <stdio.h>
#include <limits.h>

int main() {
    DIR *dir = opendir("Test");
    struct dirent *entry;
    struct stat st;
    char path[512];
    long total = 0;

    while ((entry = readdir(dir)) != NULL) {
        snprintf(path, sizeof(path), "Test/%s", entry->d_name);
        if (stat(path, &st) == 0 && S_ISREG(st.st_mode)) {
            total += st.st_size;
        }
    }
    closedir(dir);
    printf("Total size: %ld bytes\n", total);
    return 0;
}




# recursively copy all files and directories from one directory to another


##  Program Code

c
// C program to recursively copy all files and directories from one directory to another 

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/stat.h>
#include <dirent.h>
#include <fcntl.h>
#include <unistd.h>

void copy_file(const char *src, const char *dest) {
    int in = open(src, O_RDONLY);
    if (in == -1) {
        perror("open src");
        return;
    }

    int out = open(dest, O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (out == -1) {
        perror("open dest");
        close(in);
        return;
    }

    char buf[1024];
    ssize_t bytes;
    while ((bytes = read(in, buf, sizeof(buf))) > 0) {
        write(out, buf, bytes);
    }

    close(in);
    close(out);
}

void copy_directory(const char *src_dir, const char *dest_dir) {
    mkdir(dest_dir, 0755);
    DIR *dir = opendir(src_dir);
    if (!dir) {
        perror("opendir");
        return;
    }

    struct dirent *entry;
    char src_path[1024], dest_path[1024];

    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0)
            continue;

        snprintf(src_path, sizeof(src_path), "%s/%s", src_dir, entry->d_name);
        snprintf(dest_path, sizeof(dest_path), "%s/%s", dest_dir, entry->d_name);

        struct stat st;
        if (stat(src_path, &st) == 0) {
            if (S_ISDIR(st.st_mode)) {
                copy_directory(src_path, dest_path); // recursion
            } else {
                copy_file(src_path, dest_path);
            }
        }
    }

    closedir(dir);
}

int main() {
    const char *source = "Test";
    const char *destination = "temp";

    copy_directory(source, destination);
    printf("Directory copied successfully.\n");
    return 0;
}




# get the number of files in a directory named "images"


##  Program Code

c
// program to get the number of files in a directory named "images"

#include <stdio.h>
#include <dirent.h>
#include <sys/stat.h>
#include <string.h>

int main() {
    DIR *dir = opendir("images");
    if (!dir) {
        perror("opendir");
        return 1;
    }

    struct dirent *entry;
    struct stat st;
    char path[512];
    int count = 0;

    while ((entry = readdir(dir)) != NULL) {
        snprintf(path, sizeof(path), "images/%s", entry->d_name);
        if (stat(path, &st) == 0 && S_ISREG(st.st_mode)) {
            count++;
        }
    }

    closedir(dir);
    printf("Number of regular files in 'images': %d\n", count);
    return 0;
}




# Create a FIFO named "myfifo" in current directory


##  Program Code

c
// Create a FIFO named "myfifo" in current directory

#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>

int main() {
    if (mkfifo("myfifo", 0666) == -1) {
        perror("mkfifo");
        return 1;
    }

    printf("FIFO 'myfifo' created successfully.\n");
    return 0;
}




# f29.c – C Program


##  Read data from FIFO "myfifo"

c
//Read data from FIFO "myfifo"

#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    char buffer[1024];
    int fd = open("myfifo", O_RDONLY);
    if (fd == -1) {
        perror("open");
        return 1;
    }

    ssize_t bytesRead;
    while ((bytesRead = read(fd, buffer, sizeof(buffer) - 1)) > 0) {
        buffer[bytesRead] = '\0';  // Null-terminate
        printf("Received: %s", buffer);
    }

    close(fd);
    return 0;
}




# f30.c – C Program


##  Truncate "file.txt" to a specific length (e.g., 50 bytes)
c
// Truncate "file.txt" to a specific length (e.g., 50 bytes)

#include <stdio.h>
#include <unistd.h>

int main() {
    if (truncate("file.txt", 100) == -1) {
        perror("truncate");
        return 1;
    }

    printf("file.txt truncated to 100 bytes.\n");
    return 0;
}





# search for a specific string in a file named "data.txt" and display the line number(s) where it occurs

##  Program Code

c
// program to search for a specific string in a file named "data.txt" and display the line number(s) where it occurs

#include <stdio.h>
#include <string.h>

int main() {
    FILE *file = fopen("file.txt", "r");
    if (!file) {
        perror("fopen");
        return 1;
    }

    char line[1024];
    char target[] = "POSIX";  // Change this to the string to find
    int line_number = 0;

    while (fgets(line, sizeof(line), file)) {
        line_number++;
        if (strstr(line, target)) {
            printf("String found at line: %d\n", line_number);
        }
    }

    fclose(file);
    return 0;
}





# get the file type (regular file, directory, symbolic link, etc.) of a given path
##  Program Code

c
// program to get the file type (regular file, directory, symbolic link, etc.) of a given path
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>

int main(int argc, char *argv[]) {
    if (argc != 2) {
        printf("Usage: %s </home/joycy/Joycy/linux_pro/file_op>\n", argv[0]);
        return 1;
    }

    struct stat st;

    if (lstat(argv[1], &st) == -1) {
        perror("lstat");
        return 1;
    }

    if (S_ISREG(st.st_mode))
        printf("Regular file\n");
    else if (S_ISDIR(st.st_mode))
        printf("Directory\n");
    else if (S_ISLNK(st.st_mode))
        printf("Symbolic link\n");
    else if (S_ISCHR(st.st_mode))
        printf("Character device\n");
    else if (S_ISBLK(st.st_mode))
        printf("Block device\n");
    else if (S_ISFIFO(st.st_mode))
        printf("FIFO (named pipe)\n");
    else if (S_ISSOCK(st.st_mode))
        printf("Socket\n");
    else
        printf("Unknown type\n");

    return 0;
}
/*

#include <sys/stat.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    struct stat st;
    if (lstat("/home/joycy/Joycy/linux_pro/file_op", &st) == -1) {
        perror("lstat");
        return 1;
    }

    if (S_ISREG(st.st_mode)) write(1, "Regular File\n", 13);
    else if (S_ISDIR(st.st_mode)) write(1, "Directory\n", 10);
    else if (S_ISLNK(st.st_mode)) write(1, "Symlink\n", 8);
    else write(1, "Other\n", 6);
    return 0;
}
*/



# Create a New Empty File named "empty.txt"
##  Program Code

c
// Create a New Empty File named "empty.txt"

#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("empty.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return 1;
    }
    close(fd);
    printf("empty.txt created.\n");
    return 0;
}




# Get the Permissions (mode) of a File

##  Program Code

c
// Get the Permissions (mode) of a File

#include <sys/stat.h>
#include <stdio.h>

int main() {
    struct stat st;
    if (stat("file.txt", &st) == -1) {
        perror("stat");
        return 1;
    }

    printf("Permissions: 0%o\n", st.st_mode & 0777);
    return 0;
}




# Recursively Delete a Directory and Its Contents

##  Program Code

c
// Recursively Delete a Directory and Its Contents

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <dirent.h>
#include <sys/stat.h>

void delete_dir(const char *path) {
    DIR *dir = opendir(path);
    if (!dir) return;

    struct dirent *entry;
    char fullpath[512];

    while ((entry = readdir(dir))) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0)
            continue;

        snprintf(fullpath, sizeof(fullpath), "%s/%s", path, entry->d_name);
        struct stat st;
        if (stat(fullpath, &st) == -1) continue;

        if (S_ISDIR(st.st_mode))
            delete_dir(fullpath);
        else
            unlink(fullpath); // delete file
    }

    closedir(dir);
    rmdir(path); // remove the directory itself
}

int main() {
    delete_dir("temp");
    printf("Temp directory deleted.\n");
    return 0;
}




# Read and Display First 10 Lines of "log.txt"

##  Program Code

c
// Read and Display First 10 Lines of "log.txt"
/*
#include <stdio.h>

int main() {
    FILE *fp = fopen("log.txt", "r");
    if (!fp) {
        perror("fopen");
        return 1;
    }

    char line[512];
    int count = 0;
    while (fgets(line, sizeof(line), fp) && count < 10) {
        printf("%s", line);
        count++;
    }

    fclose(fp);
    return 0;
}
*/

#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("log.txt", O_RDONLY);
    if (fd < 0) return 1;

    char c;
    int lines = 0;
    while (read(fd, &c, 1) > 0 && lines < 10) {
        write(1, &c, 1);
        if (c == '\n') lines++;
    }
    close(fd);
    return 0;
}




# Copy "input.txt" to "output.txt" in Reverse Order (Line-wise)
##  Program Code

c
//Copy "input.txt" to "output.txt" in Reverse Order (Line-wise)

#include <stdio.h>
#include <stdlib.h>

int main() {
    FILE *fin = fopen("log.txt", "rb");
    FILE *fout = fopen("output.txt", "w");

    if (!fin || !fout) {
        perror("File open error");
        return 1;
    }

    // Seek to end of input file
    fseek(fin, 0, SEEK_END);
    long filesize = ftell(fin);  // get file size
    rewind(fin); // reset to start (optional here)

    // Read whole file into buffer
    char *buffer = malloc(filesize);
    if (!buffer) {
        perror("Memory allocation failed");
        fclose(fin);
        fclose(fout);
        return 1;
    }

    fread(buffer, 1, filesize, fin);

    // Write contents in reverse to output file
    for (long i = filesize - 1; i >= 0; i--) {
        fputc(buffer[i], fout);
    }

    // Cleanup
    free(buffer);
    fclose(fin);
    fclose(fout);

    printf("Reversed file content written to output.txt\n");

    return 0;
}




# Create a directory with today's date "YYYY-MM-DD"

##  Program Code

c
//Create a directory with today's date "YYYY-MM-DD"

#include <time.h>
#include <sys/stat.h>
#include <stdio.h>

int main() {
    char dirname[64];
    time_t t = time(NULL);
    struct tm *tm = localtime(&t);
    strftime(dirname, sizeof(dirname), "%Y-%m-%d", tm);

    if (mkdir(dirname, 0755) == -1) {
        perror("mkdir");
        return 1;
    }
    printf("directory is created with today's date\n");
    return 0;
}




# Read and display binary file "binary.bin"
##  Program Code

c
//  Read and display binary file "binary.bin"

#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("binary.bin", O_RDONLY);
    if (fd < 0) return 1;

    char buf[256];
    int n;
    while ((n = read(fd, buf, sizeof(buf))) > 0)
        write(1, buf, n);

    close(fd);
    return 0;
}




# Get size of the largest file in current directory

##  Program Code

c
// Get size of the largest file in current directory

#include <dirent.h>
#include <sys/stat.h>
#include <string.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    DIR *d = opendir(".");
    struct dirent *entry;
    struct stat st;
    off_t max_size = 0;
    char largest[256] = "";

    while ((entry = readdir(d)) != NULL) {
        if (entry->d_type == DT_REG) {
            if (stat(entry->d_name, &st) == 0 && st.st_size > max_size) {
                max_size = st.st_size;
                strcpy(largest, entry->d_name);
            }
        }
    }

    dprintf(1, "Largest file: %s (%ld bytes)\n", largest, max_size);
    closedir(d);
    return 0;
}




# Check if "data.txt" is readable
##  Program Code

c
//  Check if "data.txt" is readable

#include <unistd.h>

int main() {
    if (access("data.txt", R_OK) == 0)
        write(1, "Readable\n", 9);
    else
        write(1, "Not Readable\n", 13);
    return 0;
}




# Create "Logs" directory and move all .log files into it
##  Program Code

c
//Create "Logs" directory and move all .log files into it


#include <dirent.h>
#include <unistd.h>
#include <sys/stat.h>
#include <string.h>
#include <stdio.h>

int main() {
    mkdir("Logs", 0755);
    DIR *d = opendir(".");
    struct dirent *de;

    while ((de = readdir(d)) != NULL) {
        if (strstr(de->d_name, ".log")) {
            char newpath[256];
            snprintf(newpath, sizeof(newpath), "Logs/%s", de->d_name);
            rename(de->d_name, newpath);
        }
    }

    closedir(d);
    return 0;
}




# Check if "config.ini" is writable
##  Program Code

c
//  Check if "config.ini" is writable

#include <unistd.h>

int main() {
    if (access("config.ini", W_OK) == 0)
        write(1, "Writable\n", 9);
    else
        write(1, "Not Writable\n", 13);
    return 0;
}




# Read "instructions.txt" and execute commands
##  Program Code

c
//Read "instructions.txt" and execute commands

#include <fcntl.h>
#include <unistd.h>
#include <string.h>
#include <sys/wait.h>
#include <stdio.h>

int main() {
    int fd = open("instructions.txt", O_RDONLY);
    if (fd < 0) {
        perror("open");
        return 1;
    }

    char buf[256];
    int n, i = 0;

    while ((n = read(fd, &buf[i], 1)) > 0) {
        if (buf[i] == '\n') {
            buf[i] = '\0';          // Null-terminate the command
            if (strlen(buf) > 0) {
                if (fork() == 0) {
                    execlp("/bin/sh", "sh", "-c", buf, NULL);
                    perror("execlp"); // Print error if execlp fails
                    _exit(1);
                } else {
                    wait(NULL); // Parent waits for the child to finish
                }
            }
            i = 0; // Reset buffer index for the next command
        } else {
            i++;
            if (i >= sizeof(buf) - 1) {
                fprintf(stderr, "Command too long!\n");
                return 1;
            }
        }
    }

    // Handle the last line if it doesn't end with a newline
    if (i > 0) {
        buf[i] = '\0';
        if (fork() == 0) {
            execlp("/bin/sh", "sh", "-c", buf, NULL);
            perror("execlp");
            _exit(1);
        } else {
            wait(NULL);
        }
    }

    close(fd);
    return 0;
}


/*
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("instruction.txt", O_RDONLY);
    if (fd < 0) return 1;

    char buf[256];
    int n;
    while ((n = read(fd, buf, sizeof(buf))) > 0)
        write(1, buf, n);

    close(fd);
    return 0;
}
*/



# Get number of hard links to "file.txt"
##  Program Code

c
// Get number of hard links to "file.txt"

#include <sys/stat.h>
#include <stdio.h>

int main() {
    struct stat st;
    if (stat("file.txt", &st) == 0) {
        dprintf(1, "Hard Links: %lu\n", st.st_nlink);
    } else {
        perror("stat");
    }
    return 0;
}




# Copy contents of all .txt files in a directory into "combined.txt"

##  Program Code

c
//Copy contents of all .txt files in a directory into "combined.txt"

#include <dirent.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>

int main() {
    DIR *dir = opendir(".");
    struct dirent *entry;
    int out = open("combined.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    char buf[1024];
    int fd, n;

    while ((entry = readdir(dir)) != NULL) {
        if (strstr(entry->d_name, ".txt")) {
            fd = open(entry->d_name, O_RDONLY);
            if (fd < 0) continue;
            while ((n = read(fd, buf, sizeof(buf))) > 0)
                write(out, buf, n);
            close(fd);
        }
    }
    close(out);
    closedir(dir);
    return 0;
}




# Recursively calculate total size of all files in a directory and subdirectories
##  Program Code

c
// Recursively calculate total size of all files in a directory and subdirectories

#include <stdio.h>
#include <dirent.h>
#include <sys/stat.h>
#include <string.h>
#include <unistd.h>

long get_dir_size(const char *path) {
    DIR *dir = opendir(path);
    if (!dir) return 0;
    struct dirent *entry;
    struct stat st;
    char fullpath[1024];
    long total = 0;

    while ((entry = readdir(dir))) {
        if (!strcmp(entry->d_name, ".") || !strcmp(entry->d_name, "..")) continue;
        snprintf(fullpath, sizeof(fullpath), "%s/%s", path, entry->d_name);
        if (lstat(fullpath, &st) == 0) {
            if (S_ISDIR(st.st_mode))
                total += get_dir_size(fullpath);
            else if (S_ISREG(st.st_mode))
                total += st.st_size;
        }
    }
    closedir(dir);
    return total;
}

int main() {
    printf("Total size: %ld bytes\n", get_dir_size("."));
    return 0;
}




# Get number of bytes in "data.bin"

##  Program Code

c
// Get number of bytes in "data.bin"

#include <sys/stat.h>
#include <stdio.h>

int main() {
    struct stat st;
    if (stat("data.bin", &st) == 0)
        printf("Size: %ld bytes\n", st.st_size);
    else
        perror("stat");
    return 0;
}




# Create directory with current timestamp (YYYY-MM-DD-HH-MM-SS):
##  Program Code

c
//Create directory with current timestamp (YYYY-MM-DD-HH-MM-SS):

#include <time.h>
#include <sys/stat.h>
#include <stdio.h>

int main() {
    char dirname[64];
    time_t t = time(NULL);
    struct tm *tm = localtime(&t);
    strftime(dirname, sizeof(dirname), "%Y-%m-%d-%H-%M-%S", tm);
    if (mkdir(dirname, 0755) == 0)
        printf("Directory created: %s\n", dirname);
    else
        perror("mkdir");
    return 0;
}




#  Create directory named "Documents"
##  Program Code

c
// Create directory named "Documents"


#include <sys/stat.h>
#include <stdio.h>

int main() {
    if (mkdir("Documents", 0755) == 0)
        printf("Documents directory created.\n");
    else
        perror("mkdir");
    return 0;
}




# Check if "file.txt" exists in current directory
##  Program Code

c
// Check if "file.txt" exists in current directory

#include <unistd.h>
#include <stdio.h>

int main() {
    if (access("file.txt", F_OK) == 0)
        write(1, "Exists\n", 7);
    else
        write(1, "Does not exist\n", 16);
    return 0;
}




# Open "data.txt" and display its contents
##  Program Code

c
// Open "data.txt" and display its contents

#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("data.txt", O_RDONLY);
    char buf[256];
    int n;
    if (fd < 0) return 1;

    while ((n = read(fd, buf, sizeof(buf))) > 0)
        write(1, buf, n);

    close(fd);
    return 0;
}




# Create "output.txt" and write "Hello, World!"
##  Program Code

c
// Create "output.txt" and write "Hello, World!"

#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("output.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (fd < 0) return 1;
    write(fd, "Hello, World!\n", 14);
    close(fd);
    return 0;
}





# Delete "delete_me.txt" from current directory
##  Program Code

c
//  Delete "delete_me.txt" from current directory:

#include <unistd.h>
#include <stdio.h>

int main() {
    if (unlink("delete_me.txt") == 0)
        write(1, "Deleted.\n", 9);
    else
        perror("unlink");
    return 0;
}





# Rename "old_name.txt" to "new_name.txt"

##  Program Code

c
// Rename "old_name.txt" to "new_name.txt"

#include <stdio.h>

int main() {
    if (rename("old_name.txt", "new_name.txt") == 0)
        write(1, "Renamed successfully\n", 22);
    else
        perror("rename");
    return 0;
}




# Copy contents of one file to another
##  Program Code

c
// Copy contents of one file to another:

#include <fcntl.h>
#include <unistd.h>

int main() {
    int in = open("file.txt", O_RDONLY);
    int out = open("d.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    char buf[1024];
    int n;

    if (in < 0 || out < 0) return 1;

    while ((n = read(in, buf, sizeof(buf))) > 0)
        write(out, buf, n);

    close(in);
    close(out);
    return 0;
}



# Count lines in log.txt
##  Program Code

c
//Count lines in log.txt

#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("log.txt", O_RDONLY);
    char ch;
    int count = 0;

    if (fd < 0) return 1;

    while (read(fd, &ch, 1) == 1)
        if (ch == '\n') count++;

    close(fd);
    printf("Lines: %d\n", count);
    return 0;
}




# Create symbolic link
##  Program Code

c
// Create symbolic link

#include <unistd.h>
#include <stdio.h>

int main() {
    if (symlink("t.txt","l.txt") == 0)
        write(1, "Symbolic link created\n", 23);
    else
        perror("symlink");
    return 0;
}




# Implement a C program to get the size of a file named "image.jpg"

##  Program Code

c
//Implement a C program to get the size of a file named "image.jpg"

#include <sys/stat.h>
#include <stdio.h>

int main() {
    struct stat st;
    if (stat("image.jpg.png", &st) == 0)
        printf("Size: %ld bytes\n", st.st_size);
    else
        perror("stat");
    return 0;
}





# Create "notes.txt" and write multiple lines
##  Program Code

c
// Create "notes.txt" and write multiple lines

#include <fcntl.h>
#include <unistd.h>
#include <string.h>

int main() {
    int fd = open("notes.txt", O_CREAT | O_WRONLY | O_TRUNC, 0644);
    if (fd < 0) return 1;

    char *lines[] = {
        "This is line one.\n",
        "This is line two.\n",
        "This is line three.\n"
    };
    for (int i = 0; i < 3; i++)
        write(fd, lines[i], strlen(lines[i]));

    close(fd);
    return 0;
}




# Count words in "notes.txt"
## Program Code

c
// Count words in "notes.txt"

#include <fcntl.h>
#include <unistd.h>
#include <ctype.h>
#include <stdio.h>

int main() {
    int fd = open("notes.txt", O_RDONLY);
    if (fd < 0) return 1;

    char c;
    int in_word = 0, words = 0;
    while (read(fd, &c, 1) > 0) {
        if (isspace(c)) in_word = 0;
        else if (!in_word) {
            in_word = 1;
            words++;
        }
    }
    close(fd);
    printf("Words: %d\n", words);
    return 0;
}




# Create symbolic link "shortcut.txt" to "target.txt"
##  Program Code

c
// Create symbolic link "shortcut.txt" to "target.txt"

#include <unistd.h>

int main() {
    return symlink("target.txt", "shortcut.txt");
}




# Change "important.doc" permissions to read/write for owner only
##  Program Code

c
//Change "important.doc" permissions to read/write for owner only

#include <sys/stat.h>

int main() {
    return chmod("important.doc", S_IRUSR | S_IWUSR);
}




# Get last access time of "data.txt"


##  Program Code

c
//Get last access time of "data.txt"

#include <sys/stat.h>
#include <stdio.h>
#include <time.h>

int main() {
    struct stat st;
    if (stat("data.txt", &st) == -1) return 1;

    printf("Last access: %s", ctime(&st.st_atime));
    return 0;
}




#  Display binary file in hexadecimal

##  Program Code

c
//  Display binary file in hexadecimal

#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("data.bin", O_RDONLY);
    if (fd < 0) {
        perror("open");
        return 1;
    }

    unsigned char buffer[16];
    int bytesRead;
    int offset = 0;

    while ((bytesRead = read(fd, buffer, sizeof(buffer))) > 0) {
        // Print offset
        dprintf(1, "%08x  ", offset);

        // Print hex bytes
        for (int i = 0; i < 16; i++) {
            if (i < bytesRead)
                dprintf(1, "%02x ", buffer[i]);
            else
                write(1, "   ", 3);

            if (i == 7) write(1, " ", 1);
        }

        write(1, " |", 2);
        // Print ASCII characters
        for (int i = 0; i < bytesRead; i++) {
            char c = buffer[i];
            if (c >= 32 && c <= 126)
                write(1, &c, 1);
            else
                write(1, ".", 1);
        }
        write(1, "|\n", 2);

        offset += bytesRead;
    }

    close(fd);
    return 0;
}

