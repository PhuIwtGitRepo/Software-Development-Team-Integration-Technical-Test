# Development Consultant - Integration Test

## 📌 Overview
This challenge evaluates your ability to build a **resilient, maintainable, and production-ready** integration. You are tasked with migrating legacy lab result data into a modern clinical format. Please do not spend any more than 2 hours on this test.

At the senior level, we are not just looking for a successful transformation; we are looking for **architectural maturity**: how you handle data "noise," how you manage system downtime, and how you structure your logic for long-term supportability.

---

## 🛠 The Scenario
A newly acquired clinic exports its lab results via a legacy CSV format. You must build an interface to:
1.  **Ingest:** Pick up CSV files from a local directory.
2.  **Validate & Normalize:** Handle inconsistent date formats and validate mandatory fields.
3.  **Enrich:** Look up Provider names based on IDs.
4.  **Transform:** Convert the data into **HL7 v2.5.1 ORU^R01** messages.
5.  **Transmit:** Deliver to a destination TCP/IP socket with a robust retry strategy.

---

## 📥 Input Data
Use the provided file: `lab_test_full.csv`.

### The Data Challenges:
* **Date Normalization:** Dates in the source are inconsistent (e.g., `YYYYMMDD`, `MM/DD/YYYY`, `DD/MM/YYYY`, and `YYYY-MM-DD`). Your integration must normalize these to the HL7 standard: `YYYYMMDDHHMM`.
* **Provider Lookup:** Map `ProviderID` to a local cache of Provider Names (you can make up the names)
* **Special characters:** Handle "special" characters in some of the fields

---

## 🚀 Technical Requirements

### 1. Tooling
You must use either **InterSystems Health Connect (IRIS)** or **Rhapsody Integration Engine**.

### 2. Error Handling & Resilience
* **Atomic Processing:** If a record is missing a `PatientID` or has unparseable data, it must be rejected to an error queue/folder. The rest of the file **must** continue processing.
* **Connectivity:** If the destination TCP/IP socket is unavailable, the interface should retry for **5 minutes** before moving messages to an error state. It must not block the rest of the engine.

### 3. Mapping Logic
* **PID:** Map all PID as per standard PID fields
* **OBR/OBX:** With exception to the below, map request/test/result fields to the appropriate OBR/OBX field
* **OBX-8 (Abnormal Flags):** Map the CSV `Status` using this logic:
    * `F` → `F` (Final)
    * `P` → `P` (Preliminary)
    * `X` → `C` (Corrected)

---

## 📋 Submission Guidelines
Please provide a zip file containing:

1.  **The Export:**
    * **Health Connect:** A `.xml` or `.cls` export of your Production, DTLs, and custom Classes.
    * **Rhapsody:** A `.rzn` archive of your route and components.
2.  **Documentation:** A brief `COMMENTS.md` file explaining:
    * Your approach to the different date formats (`DD/MM/YYYY`).
    * How you ensured the integration is idempotent.
    * How you would modify this to "throttle" traffic if the destination system could only handle 5 messages per second.

---

**Good luck! We look forward to seeing your solution.**
