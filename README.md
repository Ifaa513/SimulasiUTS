# CRUD Pendataan Buku dengan Java Swing 📚
Program ini merupakan implementasi operasi CRUD (Create, Read, Update, dan Delete) dalam sebuah interface pengguna grafis (GUI). Setiap data yang telah berhasil diinputkan akan disimpan dalam sebuah database di PostreSql dan ditampilkan dalam sebuah tabel GUI.
## Langkah-langkah Pembuatan Program ⚡
### 1. 💻 Membuat database di PostgreSQL untuk entitas Buku dengan atribut ISBN, Judul Buku, Tahun Terbit,dan Penerbit
    CREATE TABLE buku (
    ISBN CHAR(13) PRIMARY KEY,
    judul_buku VARCHAR(255),
    tahun_terbit INTEGER,
    penerbit VARCHAR(100)
    ) ;
### 2. 📂 Membuat project baru di Netbeans
### 3. 🖍️ Buat JFrameForm 
Berikut adalah rincian properti yang digunakan:
1. 🏷️ Gunakan label untuk judul tabel (Data Buku), ISBN, Judul buku, Tahun terbit, dan Penerbit
2. 📝 Gunakan text field untuk field data buku, change variabel menjadi txtISBN, txtJudul, txtTahun, dan txtPenerbit
3. 🔘 Gunakan button untuk tombol INSERT, UPDATE, DELETE, CLEAR, dan EXIT
4. 📊 Gunakan table untuk tampilan Data Buku
### 4. 🔗 Koneksikan dengan database 
    Connection conn;
    Statement stmt;
    PreparedStatement pstmt;

    String driver = "org.postgresql.Driver";
    String koneksi = "jdbc:postgresql://localhost:5432/PBOSimulasi";
    String user = "postgres";
    String password = "5130";
    private BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
### 5. 📊 Buat method untuk menampilkan data buku dalam bentuk tabel
        public void tampil() {
        // TODO code application login
        try (Connection conn = DriverManager.getConnection(koneksi, user, password); Statement stmt = conn.createStatement(); ResultSet rs = stmt.executeQuery("SELECT * FROM 
        buku ORDER BY ISBN")) {

            // Dapatkan model tabel yang ada
            DefaultTableModel model = (DefaultTableModel) tblData.getModel();

            // Kosongkan data tabel terlebih dahulu
            model.setRowCount(0);

            // Tambahkan data dari ResultSet ke model tabel
            while (rs.next()) {
                Object[] rowData = {rs.getString(1), rs.getString(2), rs.getString(3), rs.getString(4)};
                model.addRow(rowData);
            }
        } catch (SQLException ex) {
            // Tangani eksepsi
            System.err.println("Terjadi kesalahan: " + ex.getMessage());
        }
    }
### 6. 📥 Action Performed untuk tombol Insert
Fungsi ini berfungsi agar saat pengguna menekan tombol INSERT, data buku yang dimasukkan pada form akan disimpan ke dalam database dengan menggunakan perintah INSERT INTO. Data ini dimasukkan ke dalam tabel Buku di PostgreSQL.
       
        private void btnInsertActionPerformed(java.awt.event.ActionEvent evt) {                                          
        String ISBN, Judul_Buku, Tahun_Terbit, Penerbit;
        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(koneksi, user, password);
            conn.setAutoCommit(false);

            String sql = "INSERT INTO buku (ISBN, Judul_Buku, Tahun_Terbit, Penerbit) VALUES(?, ?, ?, ?)";
            pstmt = conn.prepareStatement(sql);

            ISBN = txtISBN.getText();
            Judul_Buku = txtJudul.getText();
            Penerbit = txtPenerbit.getText();
            Tahun_Terbit = txtTahun.getText();

            pstmt.setString(1, ISBN);
            pstmt.setString(2, Judul_Buku);
            pstmt.setInt(3, Integer.parseInt(Tahun_Terbit));
            pstmt.setString(4, Penerbit);

            pstmt.executeUpdate();
            conn.commit();

            JOptionPane.showMessageDialog(null, "Data berhasil disimpan");
        } catch (ClassNotFoundException | SQLException ex) {
            JOptionPane.showMessageDialog(null, "Terjadi kesalahan: " + ex.getMessage());
        } finally {
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        tampil();
    }  
### 7. 🔄 Action Performed untuk tombol Update
Fungsi ini berguna agar saat pengguna memilih data buku di tabel dan menekan tombol UPDATE, data buku yang sudah dimodifikasi pada form akan diperbarui di database dengan menggunakan perintah UPDATE.
        
        private void btnUpdateActionPerformed(java.awt.event.ActionEvent evt) {                                          
        String ISBN = txtISBN.getText();
        String Judul_Buku = txtJudul.getText();
        String Tahun_Terbit = txtTahun.getText();
        String Penerbit = txtPenerbit.getText();

        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(koneksi, user, password);

            String updateSql = "UPDATE buku SET judul_buku = ?, penerbit = ?, tahun_terbit=? WHERE ISBN = ?";
            pstmt = conn.prepareStatement(updateSql);

            pstmt.setString(1, Judul_Buku);
            pstmt.setString(2, Penerbit);
            pstmt.setInt(3, Integer.parseInt(Tahun_Terbit));
            pstmt.setString(4, ISBN);

            int rowsAffected = pstmt.executeUpdate();
            if (rowsAffected > 0) {
                JOptionPane.showMessageDialog(null, "Data berhasil diupdate");
            } else {
                JOptionPane.showMessageDialog(null, "Data tidak ditemukan");
            }
        } catch (ClassNotFoundException | SQLException ex) {
            JOptionPane.showMessageDialog(null, "Terjadi kesalahan: " + ex.getMessage());
        } finally {
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        tampil();
    }               
### 8. 🗑️ Action Performed untuk tombol Delete
Fungsi ini dibuat agar pengguna dapat menghapus data buku berdasarkan ISBN. Perintah DELETE FROM digunakan untuk menghapus data dari database.

        private void btnDeleteActionPerformed(java.awt.event.ActionEvent evt) {                                          
        String ISBN = txtISBN.getText();

        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(koneksi, user, password);

            String deleteSql = "DELETE FROM buku WHERE ISBN = ?";
            pstmt = conn.prepareStatement(deleteSql);

            pstmt.setString(1, ISBN);

            int rowsAffected = pstmt.executeUpdate();
            if (rowsAffected > 0) {
                JOptionPane.showMessageDialog(null, "Data berhasil dihapus");
            } else {
                JOptionPane.showMessageDialog(null, "Data tidak ditemukan");
            }
        } catch (ClassNotFoundException | SQLException ex) {
            JOptionPane.showMessageDialog(null, "Terjadi kesalahan: " + ex.getMessage());
        } finally {
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        tampil();
    }                    
### 9. 🧹 Action Performed untuk tombol Clear
Fungsi ini bekerja dengan mengosongkan semua input pada form, seperti menghapus teks di field ISBN, judul buku, penerbit, dan tahun terbit. Tujuannya adalah untuk mereset form agar siap digunakan untuk memasukkan data baru atau menghindari kesalahan input ketika form masih berisi data sebelumnya.

        private void btnClearActionPerformed(java.awt.event.ActionEvent evt) {                                         
        txtISBN.setText("");
        txtJudul.setText("");
        txtTahun.setText("");
        txtPenerbit.setText("");
        }   
### 10. ❌ Action performed untuk tombol Exit
Fungsi ini digunakan untuk memudahkan pengguna keluar dari program

        private void btnExitActionPerformed(java.awt.event.ActionEvent evt) {                                        
        System.exit(0);
### 11. 🖱️ tblDataMouseClicked 
Fungsi ini dibuat untuk memudahkan pengguna agar tidak perlu mengisi ulang data buku yang ia pilih pada baris tabel untuk di update/di delete atau sekedar dilihat saja. Fungsi ini mengambil data dari baris yang diklik oleh pengguna dan menampilkannya ke dalam form input (ISBN, judul buku, penerbit, dan tahun terbit)

        private void tblDataMouseClicked(java.awt.event.MouseEvent evt) {                                     
        int row = tblData.getSelectedRow();
        txtISBN.setText(tblData.getValueAt(row, 0).toString());
        txtJudul.setText(tblData.getValueAt(row, 1).toString());
        txtTahun.setText(tblData.getValueAt(row, 2).toString());
        txtPenerbit.setText(tblData.getValueAt(row, 3).toString());
        }     
# Penerapan 🔥



