# Flutter iOS Deployment from Windows (No Mac Required)

This guide provides a step by step walkthrough on how to sign, build, and publish a Flutter iOS application to TestFlight using a Windows PC, Android Studio, and GitHub Actions—without needing a physical Mac. 

Since there is a lack of comprehensive resources on this topic, I wanted to share my personal experience to help others accomplish this seamlessly.

---

## Stage 1: Generate a Certificate Signing Request (CSR) on Windows

To request a distribution certificate from Apple, we first need to generate a digital key and a Certificate Signing Request (CSR) file.

1. Open the Start menu, type **Git Bash**, and launch it.
2. Generate a private key by running the following command:
   ```bash
   openssl genrsa -out besat.key 2048
   ```
3. Generate the CSR file by running the following command (replace the email with your Apple ID email):
   ```bash
   openssl req -new -key besat.key -out besat.csr
   ```
