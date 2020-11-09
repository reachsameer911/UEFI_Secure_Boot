# UEFI_Secure_Boot
Commands related to testing UEFI Secure Boot

# Create a key Pair
openssl req -new -x509 -newkey rsa:2048 -subj "/CN=DB_Key/" -keyout Private_DB_Key.key -out Public_DB_Cert.crt -days 365 -nodes -sha256

# Sign the kernel
sbsign --key Private_DB_Key.key --cert Public_DB_Cert.crt --output bzImage.signed bzImage

# Print cert
openssl x509 -in Public_DB_Cert.crt -outform der -out Public_DB_Cert.der

# Create a disk:

guestfish
allocate disk1.img 100MB
run
part-disk /dev/sda mbr
mkfs vfat /dev/sda1
mount /dev/sda1 /

copy-in bzImage_Unsigned.bin /
copy-in initrd.img /

quit

# Launch Qemu and Linux:

qemu-system-x86_64 -bios uefi.bin -hda disk1.img -m 2048 -smp 2 -M pc-i440fx-2.0 -enable-kvm -net none

# UEFI Signature verification code:

DxeImageVerificationLib.c

