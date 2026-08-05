# Flutter iOS Deployment from Windows (No Mac Required)

This guide provides a step-by-step walkthrough on how to sign, build, and publish a Flutter iOS application to TestFlight using a Windows PC, Android Studio, and GitHub Actions—without needing a physical Mac. 

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
4. OpenSSL will ask you a series of questions. Fill them out as shown below (do not use special characters) and press **Enter** after each:
   * **Country Name (2 letter code):** `TR`
   * **State or Province Name (full name):** `Tekirdag`
   * **Locality Name (eg, city):** `Tekirdag`
   * **Organization Name (eg, company):** `Besat Arif Cingar`
   * **Organizational Unit Name (eg, section):** `IT`
   * **Common Name (e.g. server FQDN or YOUR name):** `Besat Arif Cingar`
   * **Email Address:** `besatt59@gmail.com`
5. Finally, it will ask for **Extra Attributes**. Apple does not require these, so leave them empty:
   * **A challenge password:** *(Leave blank and press Enter)*
   * **An optional company name:** *(Leave blank and press Enter)*

You now have a perfectly formatted `besat.csr` file ready to send to Apple.

---

## Stage 2: Configure iOS Settings in Android Studio

Open your Flutter project in Android Studio to configure the foundational iOS settings.

1. Navigate to `ios/Runner/Info.plist`.
2. Define your app's display name using the `CFBundleDisplayName` key:
   ```xml
   <key>CFBundleDisplayName</key>
   <string>AstroDash</string>
   ```
3. **Permissions:** If your app uses the camera, internet, or gallery, you *must* add permission descriptions in the `Info.plist`. (Apple will reject your TestFlight upload if these are missing).
4. **Icons:** Set up your iOS app icons. You can use the `flutter_launcher_icons` package from pub.dev to generate these automatically.

---

## Stage 3: Register Your App ID

We need to register your app's name and Bundle ID with Apple.

1. Go to [developer.apple.com](https://developer.apple.com) and log in.
2. Navigate to **Certificates, Identifiers & Profiles**.
3. Select **Identifiers** from the left menu and click the blue **+ (plus)** button.
4. Select **App IDs** and click **Continue**.
5. Select **App** as the type and click **Continue**.
6. Fill out the form:
   * **Description:** Enter your app's name (e.g., `deneme`).
   * **Bundle ID:** Select **Explicit** and enter your bundle identifier (e.g., `com.besat.deneme`).
7. Click **Continue** and then **Register**. 

---

## Stage 4: Get the iOS Distribution Certificate (.cer)

Now, we will upload the CSR file we created earlier to get an approved certificate from Apple.

1. On the Apple Developer portal, click **Certificates** on the left menu.
2. Click the blue **+ (plus)** button.
3. Under the *Software* section, select **Apple Distribution** and click **Continue**.
4. Click **Choose File** and upload the `besat.csr` file you created on your desktop. Click **Continue**.
5. Click **Download** to save the `ios_distribution.cer` file. Move this file into the same folder as your `.key` and `.csr` files.

---

## Stage 5: Generate the .p12 File on Windows

Apple’s `.cer` file needs to be combined with our private key to create a `.p12` file, which is required for signing the app.

1. Open **Git Bash** in the folder containing your certificate files.
2. Convert the Apple certificate to a readable PEM format:
   ```bash
   openssl x509 -in ios_distribution.cer -inform DER -out ios_distribution.pem -outform PEM
   ```
3. Combine your key and the PEM file to create the `.p12` file:
   ```bash
   openssl pkcs12 -export -inkey besat.key -in ios_distribution.pem -out Certificate.p12
   ```
   *(If the above command fails, try adding the legacy flag:)*
   ```bash
   openssl pkcs12 -export -inkey besat.key -in ios_distribution.pem -out Certificate.p12 -legacy
   ```
4. You will be prompted to enter an **Export Password**. Type a secure password (e.g., `besat123`) and press Enter. (Characters will be hidden as you type). Verify the password by typing it again. 

You now have the highly important `Certificate.p12` file.

---

## Stage 6: Create the Provisioning Profile

We need a document that tells Apple: "I am authorized to send this specific app to the store using this certificate."

1. Go back to the Apple Developer portal and select **Profiles** from the left menu.
2. Click the blue **+ (plus)** button.
3. Under the *Distribution* section, select **App Store** and click **Continue**.
4. Select your App ID from the dropdown menu (e.g., `deneme - com.besat.deneme`) and click **Continue**.
5. Select the certificate you generated in the previous steps and click **Continue**.
6. Give your profile a name (e.g., `deneme_Profile`) and click **Generate**.
7. Click **Download** and place the `.mobileprovision` file in your certificates folder.

---

## Stage 7: Generate an App-Specific Password

To avoid hardcoding your personal Apple ID password into GitHub, we need to generate a single-use API password.

1. Go to [appleid.apple.com](https://appleid.apple.com) and log in.
2. Navigate to **Sign-In and Security**.
3. Select **App-Specific Passwords**.
4. Click **Generate an app-specific password** (or the + button).
5. Name it something recognizable, like `GitHub Actions`.
6. Copy the generated password (format: `xxxx-xxxx-xxxx-xxxx`) and save it securely in a text file. You will not be able to view it again once you close the window.

---

## Stage 8: Convert Files to Base64 Text

GitHub Secrets only accept text, not files. We must convert our certificate and provisioning profile into Base64 strings.

1. Open **Git Bash** in your certificates folder.
2. Convert the `.p12` file:
   ```bash
   base64 Certificate.p12 > cert_base64.txt
   ```
3. Convert the `.mobileprovision` file:
   ```bash
   base64 deneme_Profile.mobileprovision > profile_base64.txt
   ```
You will now see two text files filled with complex alphanumeric strings.

---

## Stage 9: Add Secrets to GitHub

We will store our sensitive data securely in GitHub's vault.

1. Go to your repository on GitHub.
2. Navigate to **Settings** > **Secrets and variables** > **Actions**.
3. Click the green **New repository secret** button and add the following 6 secrets exactly as named:

| Secret Name | Secret Value |
| :--- | :--- |
| `BUILD_CERTIFICATE_BASE64` | Copy and paste the entire contents of `cert_base64.txt`. |
| `P12_PASSWORD` | The export password you set for your `.p12` file (e.g., `besat123`). |
| `BUILD_PROVISION_PROFILE_BASE64` | Copy and paste the entire contents of `profile_base64.txt`. *(Ensure there are no trailing spaces!)* |
| `KEYCHAIN_PASSWORD` | `12345678` *(This is just a temporary, arbitrary password used during the build process).* |
| `APPLE_ID` | Your Apple ID email address (e.g., `besatt59@gmail.com`). |
| `APP_SPECIFIC_PASSWORD` | The `xxxx-xxxx-xxxx-xxxx` password you generated in Step 7. |

---

## Stage 10: Create the ExportOptions.plist in Android Studio

We need to tell the build system that the app is bound for the App Store and specify which identities to use.

1. Open your project in Android Studio.
2. Right-click the `ios` folder in your project directory and select **New > File**.
3. Name the file exactly: `ExportOptions.plist`.
4. Paste the following XML into the file (be sure to replace the `teamID`, the bundle ID, and the provisioning profile name with your own details):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
    <string>app-store</string>
    <key>teamID</key>
    <string>9FMX258KA5</string>
    <key>uploadBitcode</key>
    <false/>
    <key>compileBitcode</key>
    <false/>
    <key>uploadSymbols</key>
    <true/>
    <key>provisioningProfiles</key>
    <dict>
        <key>com.besat.deneme</key>
        <string>deneme_Profile</string>
    </dict>
</dict>
</plist>
```

---

## Stage 11: Create the GitHub Actions Workflow (YML)

This file contains the instructions for GitHub's remote Mac machines to build and deploy your app.

1. In the root directory of your project (same level as `lib`, `ios`, `android`), create a new folder named `.github` (don't forget the dot).
2. Inside `.github`, create another folder named `workflows`.
3. Inside `workflows`, create a file named `ios_deploy.yml`.
4. Paste the following code into the file:

```yaml
name: iOS TestFlight Deployment

on:
  push:
    branches:
      - main # Change this to 'master' if that is your main branch name
  workflow_dispatch: # THIS ENABLES THE MANUAL "RUN" BUTTON!
  
jobs:
  build-and-deploy-ios:
    runs-on: macos-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Setup Flutter Environment
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.22.0' # Ensure this matches the Flutter version in your project

      - name: Install Apple Certificates
        env:
          BUILD_CERTIFICATE_BASE64: ${{ secrets.BUILD_CERTIFICATE_BASE64 }}
          P12_PASSWORD: ${{ secrets.P12_PASSWORD }}
          BUILD_PROVISION_PROFILE_BASE64: ${{ secrets.BUILD_PROVISION_PROFILE_BASE64 }}
          KEYCHAIN_PASSWORD: ${{ secrets.KEYCHAIN_PASSWORD }}
        run: |
          CERTIFICATE_PATH=$RUNNER_TEMP/build_certificate.p12
          PP_PATH=$RUNNER_TEMP/build_pp.mobileprovision
          KEYCHAIN_PATH=$RUNNER_TEMP/app-signing.keychain-db

          echo -n "$BUILD_CERTIFICATE_BASE64" | base64 --decode -o $CERTIFICATE_PATH
          echo -n "$BUILD_PROVISION_PROFILE_BASE64" | base64 --decode -o $PP_PATH

          security create-keychain -p "$KEYCHAIN_PASSWORD" $KEYCHAIN_PATH
          security set-keychain-settings -lut 21600 $KEYCHAIN_PATH
          security unlock-keychain -p "$KEYCHAIN_PASSWORD" $KEYCHAIN_PATH

          security import $CERTIFICATE_PATH -P "$P12_PASSWORD" -A -t cert -f pkcs12 -k $KEYCHAIN_PATH
          security list-keychain -d user -s $KEYCHAIN_PATH

          mkdir -p ~/Library/MobileDevice/Provisioning\ Profiles
          cp $PP_PATH ~/Library/MobileDevice/Provisioning\ Profiles

      - name: Build IPA File
        run: |
          flutter pub get
          flutter build ipa --release --export-options-plist=ios/ExportOptions.plist

      - name: Upload to TestFlight
        env:
          APPLE_ID: ${{ secrets.APPLE_ID }}
          APP_SPECIFIC_PASSWORD: ${{ secrets.APP_SPECIFIC_PASSWORD }}
        run: |
          xcrun altool --upload-app -type ios -f build/ios/ipa/*.ipa --username "$APPLE_ID" --password "$APP_SPECIFIC_PASSWORD"
```

*(Note: Ensure you have already created the placeholder for your application in App Store Connect before running this workflow!)*

---

## Final Step: Run the Deployment

1. Go to your repository on GitHub.
2. Click on the **Actions** tab at the top.
3. On the left sidebar, click on **iOS TestFlight Deployment**.
4. On the right side, click the **Run workflow** dropdown and click the green **Run workflow** button.

Once the workflow is triggered, an indicator will turn yellow/orange to show it is in progress. You can click on the active run to watch the logs step-by-step. If an error occurs, the logs will show you exactly where to fix it. If it successfully finishes, your iOS app file has been pushed directly to App Store Connect / TestFlight!
