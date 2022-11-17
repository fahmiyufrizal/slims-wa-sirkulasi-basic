[![N|Solid](https://github.com/fahmiyufrizal/slims-wa-sirkulasi-basic/blob/main/Screenshot%20(378).png)](#)
# SLiMS WA Sirkulasi (Basic)

Menambahkan tombol notifikasi WA untuk sirkulasi dan keterlambatan buku, code disesuaikan dari SLiMS 9.5.x

## Yang perlu diperhatikan

- Script ini awalnya dikembangkan dari SLiMS 9.4.2 namun sudah dikonversi menyesuaikan SLiMS 9.5.x . Silahkan sesuaikan dan uji coba kembali untuk SLiMS 9.4.2 atau versi di bawahnya.
- Script ini sudah digunakan untuk keperluan perpustakaan sekolah yang memiliki rata-rata transaksi sirkulasi 40 sirkulasi per minggu. Jika sirkulasi yang dilakukan cenderung sangat ramai, pertimbangkan script/plugin lain yang menggunakan WA API.
- Script ini cukup rumit terutama saat mengganti detail template WA dan saat ini belum ada GUI untuk merubahnya. Mungkin akan masuk list di pengembangan selanjutnya.

## Apa saja yang perlu dipersiapkan?
- WA Desktop/WA Web yang sudah dilogin menggunakan nomor perpustakaan/pribadi
- Notepad++ atau IDE lain yang memudahkan untuk mengubah script

SELALU BACKUP FILE ASLI SEBELUM MELAKUKAN EDITING!

## Fungsi

- Menambahkan tombol notifikasi WhatsApp di bagian sirkulasi, sehingga pustakawan dapat mengirimkan notifikasi/bukti sirkulasi sederhana yang berisikan nama dan tanggal pengembalian
- Menambahkan tombol notifikasi WhatsApp di bagian daftar keterlambatan, sehingga pustakawan dapat mengirimkan peringatan keterlambatan.

## Tutorial Pemasangan

0. Backup dulu file admin\modules\circulation\loan_list.php dan admin\modules\reporting\customs\overdue_list.php

Untuk file loan_list.php (sirkulasi)
1. Edit file admin\modules\circulation\loan_list.php menggunakan Notepad++ (atau IDE lain)
2. Cari line 77 (atau if isset), tambahkan m.member_phone di bagian query, seperti contoh dibawah ini
```sh
if (isset($_SESSION['memberID'])) {
    $memberID = trim($_SESSION['memberID']);
    $circulation = new circulation($dbs, $memberID);
    $loan_list_query = $dbs->query(sprintf("SELECT L.loan_id, b.title, ct.coll_type_name,
        i.item_code, L.loan_date, L.due_date, L.return_date, L.renewed, m.member_name, m.member_phone,
````
3. Cari line yang memuat //table header, Tambahkan table header 'Notifikasi WA' seperti contoh dibawah ini
```sh
    // table header
	$headers = array(__('Return'), __('Extend'), __('Notifikasi WA'), __('Item Code'), __('Title'), __('Col. Type'), __('Loan Date'), __('Due Date'));
    $loan_list->setHeader($headers);
````
4. Di line sebelum //row colums array buat baris baru, tambahkan "fungsi" untuk tombol yang akan diberikan di kolom notifikasi WA

(Catatan : Di dalamnya terdapat template untuk wa.me yang akan dikirimkan ke pemustaka, silahkan ubah detail untuk nama perpustakaan, sosial media, dsb.)
```sh
		//wa notif - fahmiyufrizal
		$wa_notif = '<a href="https://wa.me/'.$loan_list_data['member_phone'].'?text=Hi, *'.$loan_list_data['member_name'].'*%0a%0aData peminjaman buku anda telah tercatat di basis data kami.%0aUntuk pengembalian buku bisa dilakukan sebelum tanggal *'.$loan_list_data['due_date'].'*%0aUntuk perpanjangan peminjaman, silahkan hubungi pustakawan sebelum tanggal *'.$loan_list_data['due_date'].'* melalui pesan WhatsApp ini.%0a%0aTerima Kasih!%0a%0aPerpustakaan Bina Insan Cendekia%0aSMPN 7 Madiun%0aIG : @smpn7official.library%0aperpustakaan.smpn7madiun.sch.id "target="_blank" class="btn btn-success btn-sm">'.__('Peminjaman').'</a><br></br><a href="https://wa.me/'.$loan_list_data['member_phone'].'?text=Hi, *'.$loan_list_data['member_name'].'*%0a%0aBuku berhasil diperpanjang!%0aUntuk pengembalian buku bisa dilakukan sebelum tanggal *'.$loan_list_data['due_date'].'*%0a%0aTerima Kasih!%0a%0aPerpustakaan Bina Insan Cendekia%0aSMPN 7 Madiun%0aIG : @smpn7official.library%0aperpustakaan.smpn7madiun.sch.id "target="_blank" class="btn btn-info btn-sm">'.__('Perpanjangan').'</a><br></br> <a href="https://wa.me/'.$loan_list_data['member_phone'].'?text=Hi, *'.$loan_list_data['member_name'].'*%0a%0aTerdapat data keterlambatan peminjaman buku dalam basis data kami. Untuk melakukan perpanjangan buku, silahkan hubungi pustakawan di Perpustakaan.%0a%0aTerima Kasih.%0a%0aPerpustakaan Bina Insan Cendekia%0aSMPN 7 Madiun%0aIG : @smpn7official.library%0aperpustakaan.smpn7madiun.sch.id "target="_blank"class="btn btn-danger btn-sm">'.__('Keterlambatan').'</a>';	
````

5. Cari line yang memuat //row colums array, Ubah row coloumn array agar tombol bisa diletakkan dengan benar, seperti ini
```sh
// row colums array
		// added array for notifikasi wa - fahmiyufrizal
        $fields = array(
            $return_link,
            $extend_link,
			$wa_notif, 
            $loan_list_data['item_code'],
            $loan_list_data['title'],
            $loan_list_data['coll_type_name'],
            $loan_list_data['loan_date'],
            $loan_list_data['due_date']
            );
````
6. Cari line yang memuat //set HTML Attributes, tambahkan kolom untuk Notifikasi WA, seperti ini
```sh
// set the HTML attributes
		// added html attributes for notifikasi wa coloumn - fahmiyufrizal
        $loan_list->setCellAttr($row, null, "valign='top' class='$row_class'");
        $loan_list->setCellAttr($row, 0, "valign='top' align='center' class='$row_class' style='width: 5%;'");
		$loan_list->setCellAttr($row, 1, "valign='top' align='center' class='$row_class' style='width: 5%;'");
        $loan_list->setCellAttr($row, 2, "valign='top' align='center' class='$row_class' style='width: 5%;'");
        $loan_list->setCellAttr($row, 3, "valign='top' class='$row_class' style='width: 10%;'"); // digunakan untuk notifikasi WA
        $loan_list->setCellAttr($row, 4, "valign='top' class='$row_class' style='width: 50%;'");
        $loan_list->setCellAttr($row, 5, "valign='top' class='$row_class' style='width: 15%;'");	
````
7. Simpan script, uji coba

Berikut ini instruksi untuk overdue_list.php (daftar keterlambatan)

1. Edit file admin\modules\reporting\customs\overdue_list.php menggunakan Notepad++ (atau IDE lain)
2. Scroll ke line 195, ubah
```sh
        $_buffer .= __('Phone Number') . ': ' . $member_d[2] . '</div>';
````
menjadi
```sh
		$_buffer .= __('Phone Number') . ': ' . $member_d[2] . ' - <a href="https://wa.me/'.$member_d[2].'?text=Hi, *'.$member_d[0].'*%0a%0aTerdapat data keterlambatan peminjaman buku dalam basis data kami. Untuk melakukan perpanjangan buku, silahkan hubungi pustakawan di Perpustakaan.%0a%0aTerima Kasih.%0a%0aPerpustakaan Bina Insan Cendekia%0aSMPN 7 Madiun%0aIG : @smpn7official.library%0aperpustakaan.smpn7madiun.sch.id "target="_blank"> Kirim Notifikasi via WA</a></div>';
````
(Catatan : Di dalamnya terdapat template untuk wa.me yang akan dikirimkan ke pemustaka, silahkan ubah detail untuk nama perpustakaan, sosial media, dsb.)

3. Simpan, uji coba

## Terimakasih Kepada

- Allah SWT
- Senayan Developer Community
- SLiMS Kudus
- Semua yang telah memanfaatkan project dan memberikan feedback ^^
