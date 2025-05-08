#!/bin/sh

main_menu() {
    clear
    echo "==========================="
    echo "     RedEye V1.0 Menu      "
    echo "==========================="
    echo "1. Encrypt a file"
    echo "2. Decrypt a file"
    echo "3. Encrypt a password"
    echo "4. Decrypt a password"
    echo "5. Exit"
    echo "==========================="
    printf "Select an option: "
    read choice

    case "$choice" in
        1) encrypt_file ;;
        2) decrypt_file ;;
        3) encrypt_password ;;
        4) decrypt_password ;;
        5) echo "Exiting RedEye..."; exit 0 ;;
        *) echo "Invalid choice. Try again."; sleep 2; main_menu ;;
    esac
}

encrypt_file() {
    printf "Enter the path to the file you want to encrypt: "
    read filepath

    if [ -f "$filepath" ]; then
        echo "File is valid."
        openssl enc -aes-256-cbc -salt -in "$filepath" -out temp_encrypted_file -pass pass:defaultkey && mv temp_encrypted_file "$filepath"
        echo "File encrypted successfully."
    else
        echo "Error: File not found."
    fi
    sleep 2
    main_menu
}

decrypt_file() {
    printf "Enter the path to the file you want to decrypt: "
    read filepath

    if [ -f "$filepath" ]; then
        echo "File is valid."
        openssl enc -aes-256-cbc -d -in "$filepath" -out temp_decrypted_file -pass pass:defaultkey && mv temp_decrypted_file "$filepath"
        echo "File decrypted successfully."
    else
        echo "Error: File not found."
    fi
    sleep 2
    main_menu
}

encrypt_password() {
    while true; do
        printf "Enter the password to encrypt (or type 'done' to return): "
        read password

        if [ "$password" = "done" ]; then
            main_menu
            break
        fi

        echo "$password" | openssl enc -aes-256-cbc -a -salt -pass pass:defaultkey
    done
}

decrypt_password() {
    while true; do
        printf "Enter the encrypted password (or type 'done' to return): "
        read enc_password

        if [ "$enc_password" = "done" ]; then
            main_menu
            break
        fi

        echo "$enc_password" | openssl enc -aes-256-cbc -a -d -pass pass:defaultkey
    done
}

main_menu
