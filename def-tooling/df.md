# Digital Forensics Fundamentals

## 🔬 Overview

Digital forensics is the application of **scientific investigation methods to digital systems and evidence**. It is used to investigate security incidents, cybercrime, and data breaches — collecting, preserving and analysing digital evidence in a way that is legally admissible.

Digital forensics sits at the intersection of cybersecurity and law enforcement. A core principle is that **evidence must never be tampered with** — the integrity of the original data must be preserved throughout the entire investigation process.

---

## 📋 Methodology (NIST Framework)

| Phase | Description |
|-------|-------------|
| **Collection** | Identify devices from which data can be collected. Ensure data is not tampered with and maintain a proper document containing each item's details |
| **Examination** | Filter the collected data and extract items of interest |
| **Analysis** | Correlate multiple pieces of evidence to draw conclusions. Aim to reconstruct activities in **chronological order** |
| **Reporting** | Prepare a detailed report containing the investigation's methodology and findings, then present to law enforcement or executives |

---

## 🗂️ Types of Digital Forensics

| Type | Description |
|------|-------------|
| **Computer Forensics** | Investigating computers — files, logs, deleted data, user activity |
| **Mobile Forensics** | Investigating mobile devices — call records, texts, GPS locations, app data |
| **Database Forensics** | Investigating intrusions into databases — queries, access logs, modified records |
| **Cloud Forensics** | Investigating data stored on cloud infrastructure — access logs, storage, APIs |
| **Email Forensics** | Investigating emails to determine if they are part of phishing or fraudulent campaigns |

---

## 🔏 Evidence Acquisition

Evidence must be collected **without tampering with the original data** — any alteration can render it inadmissible in court.

| Principle | Description |
|-----------|-------------|
| **Proper Authorisation** | Obtain authorisation from relevant authorities before collecting data — unauthorised collection can be inadmissible in court |
| **Write Blockers** | Hardware or software that blocks any write actions to the evidence — prevents accidental alteration |

### Chain of Custody

A **formal document** tracking the evidence from collection to presentation. Must include:

| Field | Description |
|-------|-------------|
| Evidence description | Name, type, identifier |
| Collector | Name of individual(s) who collected the evidence |
| Date & time | When the evidence was collected |
| Storage location | Where each piece of evidence is stored |
| Access log | Every time someone accessed the evidence and who did so |

> **Template:** [NIST Sample Chain of Custody Form](https://www.nist.gov/document/sample-chain-custody-formdocx)

---

## 🪟 Windows Forensics

### Image Types

| Type | Description | Volatility |
|------|-------------|------------|
| **Disk Image** | Bit-for-bit copy of all data on a storage device (HDD, SSD) | Non-volatile — persists after shutdown |
| **Memory Image** | Snapshot of RAM — open files, running processes, network connections | Volatile — lost on shutdown |

### Tools

| Tool | Description |
|------|-------------|
| **FTK Imager** | Takes disk images of Windows OS — used for both acquisition and analysis |
| **Autopsy** | Open-source digital forensics platform — upload a disk image and it conducts extensive automated analysis |
| **DumpIt** | Takes memory images from a Windows OS via command line |
| **Volatility** | Open-source tool for analysing memory images — extract processes, connections, artefacts |

---

## 🧪 Forensics in Practice — Metadata Analysis

Metadata is data embedded within files by the OS or application — often revealing who created a file, when, and with what device.

> Most metadata is maintained when a file is exported to other formats.

### PDF Metadata

```bash
# Install
sudo apt install poppler-utils

# Read PDF metadata (title, subject, author, creator, creation date, etc.)
pdfinfo documentName.pdf
```

### Image EXIF Data

**EXIF (Exchangeable Image File Format)** — information embedded in image files such as camera model, date/time of capture, photo settings, and GPS coordinates.

```bash
# Install
sudo apt install libimage-exiftool-perl

# Read EXIF data from an image
exiftool imageName.jpg
```

**Example EXIF fields of forensic interest:**

| Field | What It Reveals |
|-------|----------------|
| `GPS Latitude / Longitude` | Location where the photo was taken |
| `Date/Time Original` | When the photo was captured |
| `Camera Model` | Device used to take the photo |
| `Software` | Software used to edit or process the image |
| `Author` / `Creator` | Identity metadata embedded by the application |

> **Forensic tip:** GPS coordinates embedded in photos can be used to geolocate a suspect or verify/disprove an alibi. Always check EXIF before dismissing image evidence.
