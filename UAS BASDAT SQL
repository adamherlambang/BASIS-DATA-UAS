USE Harmony_Life;

CREATE TABLE pengguna (
    id_pengguna INT AUTO_INCREMENT PRIMARY KEY,
    nama_lengkap VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    kata_sandi VARCHAR(255) NOT NULL,
    tanggal_lahir DATE NOT NULL,
    role ENUM('admin', 'manager', 'auditor', 'regular_user') NOT NULL DEFAULT 'regular_user'
);

CREATE TABLE aktivitas (
    id_aktivitas INT AUTO_INCREMENT PRIMARY KEY,
    id_pengguna INT NOT NULL,
    nama_aktivitas VARCHAR(100) NOT NULL,
    deskripsi TEXT,
    prioritas ENUM('Rendah', 'Sedang', 'Tinggi') NOT NULL,
    tanggal DATE NOT NULL,
    FOREIGN KEY (id_pengguna) REFERENCES pengguna(id_pengguna)
);

CREATE TABLE preferensi_kesehatan (
    id_preferensi INT AUTO_INCREMENT PRIMARY KEY,
    id_pengguna INT NOT NULL,
    tujuan_olahraga VARCHAR(255) NOT NULL,
    kebutuhan_nutrisi TEXT NOT NULL,
    FOREIGN KEY (id_pengguna) REFERENCES pengguna(id_pengguna)
);
CREATE TABLE preferensi_keluarga (
    id_preferensi_keluarga INT AUTO_INCREMENT PRIMARY KEY,
    id_pengguna INT NOT NULL,
    aktivitas_keluarga TEXT NOT NULL,
    frekuensi ENUM('Harian', 'Mingguan', 'Bulanan') NOT NULL,
    FOREIGN KEY (id_pengguna) REFERENCES pengguna(id_pengguna)
);
INSERT INTO pengguna (nama_lengkap, email, kata_sandi, tanggal_lahir, role)
VALUES 
('Maya Suryani Putri', 'maya@example.com', 'securepass', '1985-06-15', 'manager'),
('Admin IT', 'admin@example.com', 'adminpass', '1980-01-01', 'admin'),
('Auditor Sistem', 'auditor@example.com', 'auditorpass', '1975-12-01', 'auditor'),
('Pengguna Biasa 2', 'user2@example.com', 'userpass2', '1992-08-20', 'regular_user'),
('Pengguna Biasa 3', 'user3@example.com', 'userpass3', '1995-05-15', 'regular_user');
INSERT INTO aktivitas (id_pengguna, nama_aktivitas, deskripsi, prioritas, tanggal)
VALUES 
(1, 'Olahraga Pagi', 'Lari pagi bersama keluarga', 'Tinggi', '2024-01-01'),
(5, 'Membaca Buku', 'Membaca buku cerita anak', 'Sedang', '2024-01-02');
INSERT INTO preferensi_kesehatan (id_pengguna, tujuan_olahraga, kebutuhan_nutrisi)
VALUES 
(1, 'Meningkatkan kebugaran tubuh', 'Diet seimbang, kaya serat dan protein'),
(5, 'Menjaga berat badan ideal', 'Asupan kalori sesuai kebutuhan harian');
INSERT INTO preferensi_keluarga (id_pengguna, aktivitas_keluarga, frekuensi)
VALUES 
(1, 'Jalan bersama keluarga', 'Mingguan'),
(5, 'Bermain bersama anak', 'Harian');

UPDATE pengguna
SET nama_lengkap = 'Maya Suryani Putri Updated'
WHERE email = 'maya@example.com';

CREATE VIEW ringkasan_harmoni AS
SELECT 
    p.nama_lengkap, 
    a.nama_aktivitas, 
    a.tanggal, 
    pk.tujuan_olahraga, 
    pf.aktivitas_keluarga, 
    pf.frekuensi
FROM pengguna p
LEFT JOIN aktivitas a ON p.id_pengguna = a.id_pengguna
LEFT JOIN preferensi_kesehatan pk ON p.id_pengguna = pk.id_pengguna
LEFT JOIN preferensi_keluarga pf ON p.id_pengguna = pf.id_pengguna;


DELIMITER $$
CREATE PROCEDURE TambahPengguna (
    IN nama VARCHAR(100),
    IN email VARCHAR(100),
    IN password VARCHAR(255),
    IN tanggal_lahir DATE
)
BEGIN
    INSERT INTO pengguna (nama_lengkap, email, kata_sandi, tanggal_lahir)
    VALUES (nama, email, password, tanggal_lahir);
END $$
DELIMITER ;

CALL TambahPengguna('Pengguna Biasa 3', 'user4@example.com', 'userpass4', '1993-03-15');

GRANT ALL PRIVILEGES ON Harmony_Life.* TO 'admin_user'@'localhost';

CREATE USER 'admin_user'@'localhost' IDENTIFIED BY 'adminpassword';
CREATE USER 'manager_user'@'localhost' IDENTIFIED BY 'managerpassword';

GRANT SELECT, INSERT, UPDATE ON Harmony_Life.pengguna TO 'manager_user'@'localhost';
GRANT SELECT, INSERT, UPDATE ON Harmony_Life.aktivitas TO 'manager_user'@'localhost';
GRANT SELECT, INSERT, UPDATE ON Harmony_Life.preferensi_keluarga TO 'manager_user'@'localhost';
GRANT SELECT ON Harmony_Life.ringkasan_harmoni TO 'manager_user'@'localhost';

GRANT ALL PRIVILEGES ON Harmony_Life.* TO 'admin_user'@'localhost';

SHOW GRANTS FOR 'manager_user'@'localhost';
SHOW GRANTS FOR 'admin_user'@'localhost';


USE Harmony_Life;

CREATE TABLE log_aktivitas (
    id_log INT AUTO_INCREMENT PRIMARY KEY,
    id_pengguna INT,
    aktivitas VARCHAR(255),
    waktu TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

DELIMITER $$
CREATE TRIGGER log_aktivitas
AFTER INSERT ON aktivitas
FOR EACH ROW
BEGIN
    INSERT INTO log_aktivitas (id_pengguna, aktivitas)
    VALUES (NEW.id_pengguna, NEW.nama_aktivitas);
END $$
DELIMITER ;

DELIMITER $$

CREATE PROCEDURE ProcessPreferensiKeluarga()
BEGIN
    -- Deklarasi variabel untuk cursor
    DECLARE done INT DEFAULT 0;
    DECLARE v_id_pengguna INT;
    DECLARE v_aktivitas_keluarga TEXT;
    DECLARE v_frekuensi ENUM('Harian', 'Mingguan', 'Bulanan');

    -- Deklarasi cursor untuk mengambil data dari tabel preferensi_keluarga
    DECLARE cur_preferensi CURSOR FOR 
        SELECT id_pengguna, aktivitas_keluarga, frekuensi
        FROM preferensi_keluarga;

    -- Handler untuk menandai jika cursor telah selesai
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    -- Buka cursor untuk mulai mengambil data
    OPEN cur_preferensi;
    -- Looping untuk mengambil dan memproses data
    read_loop: LOOP
        -- Ambil data dari cursor dan simpan dalam variabel
        FETCH cur_preferensi INTO v_id_pengguna, v_aktivitas_keluarga, v_frekuensi;

        -- Jika tidak ada data lagi, keluar dari loop
        IF done THEN
            LEAVE read_loop;
        END IF;
        SELECT v_id_pengguna AS PenggunaID, v_aktivitas_keluarga AS Aktivitas, v_frekuensi AS Frekuensi;
    END LOOP;
    -- Tutup cursor setelah selesai
    CLOSE cur_preferensi;
END $$
DELIMITER ;
CALL ProcessPreferensiKeluarga();



DELIMITER $$
CREATE PROCEDURE TestErrorHandling()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE v_id_pengguna INT;

    -- Deklarasi cursor dengan query yang salah (kolom tidak ada)
    DECLARE cur_preferensi CURSOR FOR 
        SELECT id_pengguna, non_existent_column
        FROM preferensi_keluarga;

    -- Handler untuk error SQL
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        -- Menampilkan pesan error
        SELECT 'Terjadi kesalahan saat memproses data!' AS ErrorMessage;
        ROLLBACK;  -- Jika ada transaksi, rollback perubahan yang dilakukan
    END;
    -- Mulai transaksi jika diperlukan
    START TRANSACTION;
    -- Buka cursor untuk mulai mengambil data
    OPEN cur_preferensi;
    -- Looping untuk mengambil dan memproses data
    read_loop: LOOP
        FETCH cur_preferensi INTO v_id_pengguna;
        IF done THEN
            LEAVE read_loop;
        END IF;
    END LOOP;
    CLOSE cur_preferensi;
    COMMIT;
END $$
DELIMITER ;
CALL TestErrorHandling();
