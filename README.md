# NetSpot v6.0.0.780

Build of NetSpot v6.0.0.780 with patched digital signature integrity checks and unlocked Pro features.

Pre-packaged release archive is available in [GitHub Releases](https://github.com/bagas39/netspotpro/releases): `NetSpot_v6.0.0.780.zip`.

---

## How To Reproduce

Running patched .NET assemblies invalidates their original strong name signature, causing the CLR to throw `FileLoadException`. Run the following commands in an elevated Command Prompt (Run as administrator) before launching the application:

```cmd
reg add "HKLM\Software\Microsoft\StrongName\Verification\NetSpot.Core.Activation,*" /f
reg add "HKLM\Software\Wow6432Node\Microsoft\StrongName\Verification\NetSpot.Core.Activation,*" /f
```

After registering the exception, run `NetSpot.exe`.

---

## Patching Guide

Step-by-step instructions for reproducing the binary modifications using dnSpy.

### Step 1: Launch dnSpy as Administrator

1. Open Windows Start Menu and search for **dnSpy**.
2. Right-click and choose **Run as administrator**.

---

### Step 2: Patch Signature Verification in NetSpot.exe

Bypass the internal digital signature verification method in the main executable.

1. In dnSpy, go to **File > Open**.
2. Open `NetSpot.exe`.
3. In Assembly Explorer, navigate to:
   `NetSpot.exe` > `NetSpot.Models` > `DsProtector` > `CheckFilesForDigitalSignature()`
4. Right-click `CheckFilesForDigitalSignature()` and select **Edit Method (C#)...**.
5. Replace the body with:
   ```csharp
   public bool CheckFilesForDigitalSignature()
   {
       return true;
   }
   ```
6. Click **Compile**.
7. Go to **File > Save Module...** and click **OK**.

https://github.com/user-attachments/assets/ddc27e5f-f406-4ddb-b133-64b798f7953a

---

### Step 3: Patch Feature Resolution in NetSpot.Core.Activation.dll

Force all feature entitlement checks to evaluate to true.

1. In dnSpy, go to **File > Open**.
2. Open `NetSpot.Core.Activation.dll`.
3. In Assembly Explorer, navigate to:
   `NetSpot.Core.Activation.dll` > `NetSpot.Core.Activation` > `Features` > `IsFeatureResolved(bool, Feature)`
4. Right-click `IsFeatureResolved(bool, Feature)` and select **Edit Method (C#)...**.
5. Replace the body with:
   ```csharp
   private bool IsFeatureResolved(bool usage, Feature feature)
   {
       return true;
   }
   ```
6. Click **Compile**.
7. Go to **File > Save Module...** and click **OK**.





https://github.com/user-attachments/assets/f5a8dd91-b267-4a85-8e0a-38735dc8a87d


---

### Step 4: Verification (Proof of Concept)
https://github.com/user-attachments/assets/1d5c5f84-c002-4572-9c6b-67fd2ce704cf
1. Run `NetSpot.exe`.
2. Go to **Survey > Start a new survey > Draw it**.
3. Verify that survey canvas and floor plan generation tools load and function without license or signature errors.
