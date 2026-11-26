
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Absensi MAN 1 Kota Cirebon 2026</title>

<script src="https://unpkg.com/html5-qrcode"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>

<style>
*{box-sizing:border-box}
body{
 margin:0;font-family:Arial,sans-serif;
 background:#eef3f8;color:#172b3a;
}
header{
 background:#075b35;color:white;text-align:center;
 padding:22px 12px;
}
header h1{margin:0;font-size:24px}
header p{margin:8px 0 0}
.container{max-width:1150px;margin:20px auto;padding:12px}
.card{
 background:white;padding:18px;border-radius:12px;
 box-shadow:0 3px 12px #0001;margin-bottom:18px;
}
h2{color:#075b35;font-size:20px}
input,select,button{
 padding:11px;border:1px solid #ccd5df;
 border-radius:7px;font-size:14px;margin:4px 0;
}
input,select{max-width:100%}
button{
 background:#087b48;color:white;border:0;
 cursor:pointer;font-weight:bold;
}
button:hover{opacity:.85}
.danger{background:#c62828}
.blue{background:#1565c0}
.orange{background:#e58a00}
.grid{
 display:grid;grid-template-columns:repeat(auto-fit,minmax(190px,1fr));
 gap:12px;
}
.stat{
 background:#e7f5ed;padding:18px;border-radius:10px;
 text-align:center;
}
.stat strong{font-size:28px;display:block;color:#075b35}
#reader{max-width:450px;margin:15px auto}
table{width:100%;border-collapse:collapse;min-width:650px}
th,td{padding:10px;border:1px solid #ddd;text-align:left}
th{background:#075b35;color:white}
.table-wrap{overflow-x:auto}
.small{font-size:12px;color:#666}
#qrArea{
 display:flex;flex-wrap:wrap;gap:12px;margin-top:15px;
}
.qr-card{
 border:1px solid #ddd;border-radius:8px;
 padding:12px;text-align:center;width:180px;
 break-inside:avoid;
}
.qr-card canvas,.qr-card img{margin:auto}
footer{text-align:center;padding:20px;color:#666}
@media print{
 body{background:white}
 header,.no-print,.card:not(.qr-print){display:none!important}
 .qr-print{display:block!important;box-shadow:none}
 #qrArea{display:grid;grid-template-columns:repeat(3,1fr)}
 .qr-card{width:100%}
}
</style>
</head>

<body>

<header>
 <h1>ABSENSI DIGITAL SISWA</h1>
 <h2 style="color:white">MAN 1 KOTA CIREBON</h2>
 <p>TAHUN 2026 | SISTEM ABSENSI QR CODE</p>
 <p id="tanggalJam"></p>
</header>

<div class="container">

<!-- DASHBOARD -->
<div class="card">
 <h2>Dashboard Absensi</h2>
 <div class="grid">
  <div class="stat">Total Siswa<strong id="totalSiswa">0</strong></div>
  <div class="stat">Hadir Masuk<strong id="totalMasuk">0</strong></div>
  <div class="stat">Sudah Pulang<strong id="totalPulang">0</strong></div>
  <div class="stat">Belum Pulang<strong id="belumPulang">0</strong></div>
 </div>
</div>

<!-- DATABASE SISWA -->
<div class="card no-print">
 <h2>1. Database Siswa</h2>
 <div class="grid">
  <input id="nis" placeholder="Nomor Induk Siswa (NIS)">
  <input id="nama" placeholder="Nama Lengkap Siswa">
  <select id="jk">
   <option value="">Jenis Kelamin</option>
   <option value="Laki-laki">Laki-laki</option>
   <option value="Perempuan">Perempuan</option>
  </select>
  <input id="kelas" placeholder="Kelas (Contoh: XI-A)">
 </div>
 <button onclick="tambahSiswa()">SIMPAN DATA SISWA</button>
 <button class="blue" onclick="buatSemuaQR()">BUAT QR CODE SISWA</button>
 <button class="orange" onclick="document.getElementById('fileImport').click()">IMPORT DATA CSV</button>
 <input type="file" id="fileImport" accept=".csv" hidden onchange="importCSV(event)">
 <div class="small">Kolom CSV: NIS, Nama, Jenis Kelamin, Kelas.</div>
 <div class="table-wrap">
  <table>
   <thead><tr><th>No</th><th>NIS</th><th>Nama</th><th>JK</th><th>Kelas</th><th>Aksi</th></tr></thead>
   <tbody id="dataSiswa"></tbody>
  </table>
 </div>
</div>

<!-- SCANNER -->
<div class="card no-print">
 <h2>2. Scan QR Code Absensi</h2>
 <label>Pilih Jenis Absensi:</label><br>
 <select id="modeAbsensi">
  <option value="Masuk">ABSENSI MASUK SEKOLAH</option>
  <option value="Pulang">ABSENSI PULANG SEKOLAH</option>
 </select>
 <p class="small">Arahkan kamera ke QR Code kartu identitas siswa.</p>
 <div id="reader"></div>
 <button onclick="mulaiScan()">MULAI SCAN KAMERA</button>
 <button class="danger" onclick="stopScan()">STOP KAMERA</button>
 <div id="hasilScan"></div>
 <hr>
 <h3>Absensi Manual</h3>
 <input id="nisManual" placeholder="Masukkan NIS siswa">
 <button class="blue" onclick="absenManual()">PROSES ABSENSI</button>
</div>

<!-- LAPORAN -->
<div class="card">
 <h2>3. Laporan Absensi Siswa</h2>
 <div class="no-print">
  <div class="grid">
   <div>
    <label>Tanggal:</label><br>
    <input type="date" id="filterTanggal">
   </div>
   <div>
    <label>Kelas:</label><br>
    <select id="filterKelas" onchange="tampilAbsensi()">
     <option value="">Semua Kelas</option>
    </select>
   </div>
  </div>
  <button onclick="tampilAbsensi()">TAMPILKAN DATA</button>
  <button class="blue" onclick="exportCSV()">DOWNLOAD EXCEL (CSV)</button>
  <button class="orange" onclick="window.print()">CETAK LAPORAN</button>
 </div>
 <div class="table-wrap">
  <table>
   <thead>
    <tr>
     <th>No</th><th>Tanggal</th><th>NIS</th>
     <th>Nama</th><th>Kelas</th>
     <th>Jam Masuk</th><th>Status</th><th>Jam Pulang</th>
    </tr>
   </thead>
   <tbody id="dataAbsensi"></tbody>
  </table>
 </div>
</div>

<!-- QR SISWA -->
<div class="card qr-print">
 <h2>4. Kartu QR Code Siswa</h2>
 <div class="no-print">
  <button onclick="window.print()">CETAK SEMUA KARTU QR</button>
 </div>
 <div id="qrArea"></div>
</div>

</div>

<footer>
MAN 1 KOTA CIREBON | ABSENSI DIGITAL TAHUN 2026
</footer>

<script>
let siswa=JSON.parse(localStorage.getItem("siswaMAN1")||"[]");
let absensi=JSON.parse(localStorage.getItem("absenMAN1")||"[]");
let scanner=null;
let scanAktif=false;
let prosesScan=false;

const hariIni=()=>new Date().toLocaleDateString("en-CA");
const jamSekarang=()=>new Date().toLocaleTimeString("id-ID",{
 hour:"2-digit",minute:"2-digit",second:"2-digit",hour12:false
});

function simpan(){
 localStorage.setItem("siswaMAN1",JSON.stringify(siswa));
 localStorage.setItem("absenMAN1",JSON.stringify(absensi));
}

function tampilJam(){
 document.getElementById("tanggalJam").textContent=
 new Date().toLocaleString("id-ID");
}
setInterval(tampilJam,1000);
tampilJam();

function tambahSiswa(){
 let nis=document.getElementById("nis").value.trim();
 let nama=document.getElementById("nama").value.trim();
 let jk=document.getElementById("jk").value;
 let kelas=document.getElementById("kelas").value.trim();

 if(!nis||!nama||!jk||!kelas){
  alert("Lengkapi semua data siswa!");
  return;
 }
 if(siswa.some(s=>s.nis===nis)){
  alert("NIS sudah terdaftar!");
  return;
 }
 siswa.push({nis,nama,jk,kelas});
 simpan();tampilSiswa();
 document.getElementById("nis").value="";
 document.getElementById("nama").value="";
 document.getElementById("jk").value="";
 document.getElementById("kelas").value="";
}

function tampilSiswa(){
 let html="";
 siswa.forEach((s,i)=>{
 html+=`<tr>
 <td>${i+1}</td><td>${aman(s.nis)}</td>
 <td>${aman(s.nama)}</td><td>${aman(s.jk)}</td>
 <td>${aman(s.kelas)}</td>
 <td>
 <button class="blue" onclick="buatQR('${encodeURIComponent(s.nis)}')">QR</button>
 <button class="danger" onclick="hapusSiswa('${encodeURIComponent(s.nis)}')">Hapus</button>
 </td></tr>`;
 });
 document.getElementById("dataSiswa").innerHTML=html;
 updateDashboard();
 updateFilterKelas();
}

function aman(x){
 return String(x).replace(/[&<>"']/g,c=>({
 "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
 })[c]);
}

function hapusSiswa(nis){
 nis=decodeURIComponent(nis);
 if(!confirm("Hapus data siswa ini?"))return;
 siswa=siswa.filter(s=>s.nis!==nis);
 simpan();tampilSiswa();
}

function updateFilterKelas(){
 let pilih=document.getElementById("filterKelas");
 let lama=pilih.value;
 let kelas=[...new Set(siswa.map(s=>s.kelas))].sort();
 pilih.innerHTML='<option value="">Semua Kelas</option>'+
 kelas.map(k=>`<option value="${aman(k)}">${aman(k)}</option>`).join("");
 pilih.value=kelas.includes(lama)?lama:"";
}

function buatQR(nisEncoded){
 let nis=decodeURIComponent(nisEncoded);
 let s=siswa.find(x=>x.nis===nis);
 if(!s)return;
 let area=document.getElementById("qrArea");
 area.innerHTML="";
 buatKartu(s,area);
}

function buatKartu(s,area){
 let div=document.createElement("div");
 div.className="qr-card";
 let judul=document.createElement("h4");
 judul.textContent="MAN 1 KOTA CIREBON";
 let identitas=document.createElement("p");
 identitas.textContent=s.nama+" | "+s.kelas+" | "+s.nis;
 let qr=document.createElement("div");
 div.append(judul,identitas,qr);
 area.appendChild(div);
 new QRCode(qr,{
  text:s.nis,width:115,height:115,
  correctLevel:QRCode.CorrectLevel.H
 });
}

function buatSemuaQR(){
 if(!siswa.length){alert("Belum ada data siswa");return;}
 let area=document.getElementById("qrArea");
 area.innerHTML="";
 siswa.forEach(s=>buatKartu(s,area));
 area.scrollIntoView({behavior:"smooth"});
}

async function mulaiScan(){
 if(scanAktif)return;
 if(!window.Html5Qrcode){
  alert("Library QR tidak berhasil dimuat. Periksa internet.");
  return;
 }
 try{
  scanner=new Html5Qrcode("reader");
  await scanner.start(
   {facingMode:"environment"},
   {fps:10,qrbox:{width:230,height:230}},
   kode=>{
    if(prosesScan)return;
    prosesScan=true;
    prosesAbsensi(kode.trim());
    setTimeout(()=>prosesScan=false,1800);
   },
   ()=>{}
  );
  scanAktif=true;
 }catch(e){
  alert("Kamera tidak dapat dibuka. Izinkan akses kamera dan gunakan HTTPS atau localhost.");
 }
}

async function stopScan(){
 if(scanner && scanAktif){
  try{await scanner.stop();}catch(e){}
  scanner.clear();
 }
 scanAktif=false;
 scanner=null;
}

function absenManual(){
 let nis=document.getElementById("nisManual").value.trim();
 if(!nis){alert("Masukkan NIS!");return;}
 prosesAbsensi(nis);
 document.getElementById("nisManual").value="";
}

function prosesAbsensi(nis){
 let s=siswa.find(x=>x.nis===nis);
 if(!s){
  document.getElementById("hasilScan").innerHTML=
   "<p style='color:red'>NIS tidak ditemukan dalam database!</p>";
  return;
 }
 let tanggal=hariIni();
 let jam=jamSekarang();
 let mode=document.getElementById("modeAbsensi").value;

 let data=absensi.find(a=>a.nis===nis&&a.tanggal===tanggal);

 if(mode==="Masuk"){
  if(data&&data.jamMasuk){
   tampilPesan("Siswa sudah melakukan absensi masuk: "+data.jamMasuk,"orange");
   return;
  }
  let batasTerlambat="07:00:00";
  let status=jam>batasTerlambat?"Terlambat":"Hadir";
  if(!data){
   data={
    nis:s.nis,nama:s.nama,kelas:s.kelas,
    tanggal:tanggal,jamMasuk:jam,
    status:status,jamPulang:""
   };
   absensi.push(data);
  }else{
   data.jamMasuk=jam;
   data.status=status;
  }
  tampilPesan("ABSENSI MASUK BERHASIL: "+s.nama+" | "+jam+" | "+status,"green");
 }else{
  if(!data||!data.jamMasuk){
   tampilPesan("Siswa belum melakukan absensi masuk!","red");
   return;
  }
  if(data.jamPulang){
   tampilPesan("Siswa sudah melakukan absensi pulang: "+data.jamPulang,"orange");
   return;
  }
  data.jamPulang=jam;
  tampilPesan("ABSENSI PULANG BERHASIL: "+s.nama+" | "+jam,"green");
 }
 simpan();
 tampilAbsensi();
 updateDashboard();
}

function tampilPesan(pesan,warna){
 let warnaCSS=warna==="green"?"green":warna==="orange"?"#b66b00":"red";
 document.getElementById("hasilScan").innerHTML=
 `<h3 style="color:${warnaCSS}">${aman(pesan)}</h3>`;
}

function tampilAbsensi(){
 let tanggal=document.getElementById("filterTanggal").value;
 let kelas=document.getElementById("filterKelas").value;
 let data=absensi.filter(a=>
  (!tanggal||a.tanggal===tanggal)&&
  (!kelas||a.kelas===kelas)
 ).sort((a,b)=>(b.tanggal+b.jamMasuk).localeCompare(a.tanggal+a.jamMasuk));
 let html="";
 data.forEach((a,i)=>{
 html+=`<tr>
 <td>${i+1}</td><td>${aman(a.tanggal)}</td>
 <td>${aman(a.nis)}</td><td>${aman(a.nama)}</td>
 <td>${aman(a.kelas)}</td><td>${aman(a.jamMasuk||"-")}</td>
 <td>${aman(a.status||"-")}</td>
 <td>${aman(a.jamPulang||"Belum Pulang")}</td>
 </tr>`;
 });
 document.getElementById("dataAbsensi").innerHTML=
 html||'<tr><td colspan="8">Belum ada data absensi.</td></tr>';
}

function updateDashboard(){
 let t=hariIni();
 let data=absensi.filter(a=>a.tanggal===t);
 document.getElementById("totalSiswa").textContent=siswa.length;
 document.getElementById("totalMasuk").textContent=
 data.filter(a=>a.jamMasuk).length;
 document.getElementById("totalPulang").textContent=
 data.filter(a=>a.jamPulang).length;
 document.getElementById("belumPulang").textContent=
 data.filter(a=>a.jamMasuk&&!a.jamPulang).length;
}

function exportCSV(){
 let tanggal=document.getElementById("filterTanggal").value;
 let kelas=document.getElementById("filterKelas").value;
 let data=absensi.filter(a=>
  (!tanggal||a.tanggal===tanggal)&&
  (!kelas||a.kelas===kelas)
 );
 if(!data.length){alert("Tidak ada data untuk diekspor!");return;}
 let rows=[
  ["Tanggal","NIS","Nama","Kelas","Jam Masuk","Status","Jam Pulang"],
  ...data.map(a=>[
   a.tanggal,a.nis,a.nama,a.kelas,
   a.jamMasuk,a.status,a.jamPulang
  ])
 ];
 let csv="\uFEFF"+rows.map(r=>r.map(v=>
  '"'+String(v??"").replace(/"/g,'""')+'"'
 ).join(";")).join("\r\n");
 let blob=new Blob([csv],{type:"text/csv;charset=utf-8;"});
 let url=URL.createObjectURL(blob);
 let link=document.createElement("a");
 link.href=url;link.download="Absensi_MAN1_Cirebon_2026.csv";
 link.click();URL.revokeObjectURL(url);
}

function importCSV(event){
 let file=event.target.files[0];
 if(!file)return;
 let reader=new FileReader();
 reader.onload=e=>{
  let lines=e.target.result.replace(/^\uFEFF/,"").split(/\r?\n/);
  let tambah=0;
  lines.slice(1).forEach(line=>{
   if(!line.trim())return;
   let kolom=line.split(/[;,]/).map(x=>x.trim().replace(/^"|"$/g,""));
   if(kolom.length<4)return;
   let [nis,nama,jk,kelas]=kolom;
   if(nis&&nama&&!siswa.some(s=>s.nis===nis)){
    siswa.push({nis,nama,jk,kelas});tambah++;
   }
  });
  simpan();tampilSiswa();
  alert(tambah+" siswa berhasil diimpor.");
  event.target.value="";
 };
 reader.readAsText(file);
}

document.getElementById("filterTanggal").value=hariIni();
tampilSiswa();
tampilAbsensi();
</script>
<button class="danger"
onclick="hapusSemuaLaporan()">
HAPUS LAPORAN TERFILTER
</button>

<thead>
<tr>
    <th>No</th>
    <th>Tanggal</th>
    <th>NIS</th>
    <th>Nama</th>
    <th>Kelas</th>
    <th>Jam Masuk</th>
    <th>Status</th>
    <th>Jam Pulang</th>
    <th>Aksi</th>
</tr>
</thead>

</body>
</html>
