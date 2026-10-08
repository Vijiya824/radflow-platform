CREATE DATABASE IF NOT EXISTS radflow_db;
USE radflow_db;

-- 1. Users Table (Authentication & Spring Security)
CREATE TABLE IF NOT EXISTS users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    role VARCHAR(30) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 2. Patients Table
CREATE TABLE IF NOT EXISTS patients (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    patient_identifier VARCHAR(64) NOT NULL UNIQUE,
    full_name VARCHAR(128) NOT NULL,
    dob DATE NOT NULL,
    gender VARCHAR(16) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 3. Imaging Studies Table
CREATE TABLE IF NOT EXISTS imaging_studies (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    study_instance_uid VARCHAR(128) NOT NULL UNIQUE,
    patient_id BIGINT NOT NULL,
    modality VARCHAR(16) NOT NULL,
    body_part VARCHAR(32) NOT NULL,
    file_path VARCHAR(255) NOT NULL,
    urgency_status VARCHAR(16) DEFAULT 'ROUTINE',
    review_status VARCHAR(16) DEFAULT 'PENDING',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_patient FOREIGN KEY (patient_id) REFERENCES patients(id) ON DELETE CASCADE
);

-- 4. AI Findings Table
CREATE TABLE IF NOT EXISTS ai_findings (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    study_id BIGINT NOT NULL UNIQUE,
    condition_detected VARCHAR(128) NOT NULL,
    confidence_score DECIMAL(5, 4) NOT NULL,
    urgency_level VARCHAR(16) NOT NULL,
    bounding_box_json TEXT NULL,
    processed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_study FOREIGN KEY (study_id) REFERENCES imaging_studies(id) ON DELETE CASCADE
);