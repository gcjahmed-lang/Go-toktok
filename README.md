<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>توكتوك سوهاج 🛺</title>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<style>
*{box-sizing:border-box;font-family:'Segoe UI',Tahoma} body{margin:0;background:#FFF7ED;padding:15px}
.card{background:#fff;border-radius:16px;padding:16px;margin-bottom:12px;box-shadow:0 2px 10px #0001}
input,select{width:100%;padding:13px;margin:7px 0;border:1px solid #ddd;border-radius:10px;font-size:16px}
button{width:100%;padding:14px;border:none;border-radius:10px;font-size:16px;font-weight:bold;cursor:pointer;margin-top:6px}
.btn-green{background:#16A34A;color:#fff}.btn-orange{background:#F97316;color:#fff}.btn-blue{background:#2563EB;color:#fff}.btn-red{background:#DC2626;color:#fff}.btn-gray{background:#E5E7EB}.btn-black{background:#111;color:#fff}.btn-purple{background:#7C3AED;color:#fff}
.hidden{display:none} .flex{display:flex;gap:8px;flex-wrap:wrap} .flex button{flex:1;min-width:100px}
.badge{padding:4px 10px;border-radius:20px;font-size:13px;font-weight:bold}
.pending{background:#FEF3C7}.approved{background:#DCFCE7}.rejected{background:#FEE2E2}
h2{text-align:center} .top{display:flex;justify-content:space-between;align-items:center}
#map,#mapDriver{height:320px;border-radius:14px;border:1px solid #ddd;z-index:1}
.gps-box{background:#EFF6FF;border:1px solid #BFDBFE;border-radius:12px;padding:10px;margin:8px 0;font-size:14px;line-height:1.8}
.live{color:#16A34A;font-weight:bold}
.driver-coming{background:#DCFCE7;border:2px solid #16A34A;border-radius:14px;padding:12px;margin:8px 0;text-align:center}
.nav-box{background:#FFF7ED;border:2px solid #F97316;border-radius:14px;padding:12px;margin:8px 0;text-align:center}
.admin-box{background:#FEF3C7;border:2px solid #F59E0B;border-radius:14px;padding:12px;margin-top:10px}
.rider-box{background:#EFF6FF;border:2px solid #2563EB;border-radius:14px;padding:12px;margin-top:10px}
.driver-box{background:#FFF7ED;border:2px solid #F97316;border-radius:14px;padding:12px;margin-top:10px}
.tab-active{outline:3px solid #111}
</style>
</head>
<body>

<div id="authScreen">
  <h2>🛺 توكتوك سوهاج</h2>
  <p style="text-align:center;color:#666">أسرع توصيلة في سوهاج</p>
  <div class="card">
    <div class="flex">
      <button class="btn-orange" id="btn_login" onclick="showTab('login')">دخول</button>
      <button class="btn-blue" id="btn_riderRegister" onclick="showTab('riderRegister')">🧍 تسجيل راكب</button>
      <button class="btn-orange" id="btn_driverRegister" onclick="showTab('driverRegister')">🛺 تسجيل سواق</button>
      <button class="btn-black" id="btn_adminLogin" onclick="showTab('adminLogin')">👑 أدمن</button>
    </div>
    <div id="loginTab">
      <input id="loginPhone" placeholder="رقم الموبايل">
      <input id="loginPass" type="password" placeholder="كلمة السر - الراكب يكتب رقمه">
      <button class="btn-green" onclick="doLogin()">دخول 🚀</button>
      <p style="text-align:center;font-size:13px;color:#888">هتدخل مرة واحدة بس، وبعد كده التطبيق هيفتكرك على طول</p>
    </div>
    <div id="riderRegisterTab" class="hidden">
      <div class="rider-box"><h3 style="text-align:center;margin:0">🧍 تسجيل راكب جديد</h3><p style="text-align:center;font-size:13px">من غير كود - سجل على طول ✅</p></div>
      <input id="regNameRider" placeholder="الاسم بالكامل">
      <input id="regPhoneRider" placeholder="رقم الموبايل 11 رقم">
      <input id="regPassRider" type="password" placeholder="اعمل كلمة سر (او سيبها رقمك)">
      <button class="btn-green" onclick="doRiderRegister()">تأكيد تسجيل الراكب ✅</button>
    </div>
    <div id="driverRegisterTab" class="hidden">
      <div class="driver-box"><h3 style="text-align:center;margin:0">🛺 تسجيل سواق جديد</h3><p style="text-align:center;font-size:13px">من غير كود SMS - سجل على طول ✅</p></div>
      <input id="regName" placeholder="اسم السواق بالكامل">
      <input id="regPhone" placeholder="رقم موبايل السواق 11 رقم">
      <input id="regPass" type="password" placeholder="اعمل كلمة سر للسواق">
      <input id="regNational" placeholder="الرقم القومي 14 رقم">
      <select id="regGov"><option value="سوهاج">سوهاج</option></select>
      <select id="regArea"><option>سوهاج المدينة</option><option>أخميم</option><option>جرجا</option><option>طهطا</option><option>جهينة</option><option>المراغة</option><option>ساقلتة</option><option>دار السلام</option><option>البلينا</option><option>المنشاة</option><option>طما</option><option>الجلاوية</option></select>
      <input id="regTuk" placeholder="رقم التوكتوك">
      <input id="regLicense" placeholder="رقم الرخصة">
      <button class="btn-orange" onclick="doDriverRegister()">تأكيد تسجيل السواق ✅</button>
      <p style="text-align:center;font-size:12px;color:#888">بعد التسجيل، الأدمن هيقبلك او يرفضك</p>
    </div>
    <div id="adminLoginTab" class="hidden">
      <div class="admin-box"><h3 style="text-align:center;margin:0">👑 دخول الأدمن</h3><p style="text-align:center;font-size:13px">حط رقمك وكلمة السر وهيفتح على طول</p></div>
      <input id="adminLoginPhone" placeholder="رقم موبايل الأدمن">
      <input id="adminLoginPass" type="password" placeholder="كلمة سر الأدمن">
      <button class="btn-black" onclick="doAdminLogin()">دخول الأدمن 👑</button>
    </div>
  </div>
</div>

<div id="riderScreen" class="hidden">
  <div class="top"><h3>أهلا بيك يا <span id="riderName"></span> 👋</h3><button style="width:auto" class="btn-gray" onclick="logout()">خروج</button></div>
  <div id="driverComingBox" class="hidden"></div>
  <div class="card">
    <h3>🗺️ خريطة سوهاج - شوف سواقك قدامك لايف</h3>
    <div id="map"></div>
    <div class="gps-box" id="riderGpsBox">🛰️ دوس على "شغل الـ GPS" عشان نشوف مكانك ومكان السواق</div>
    <button class="btn-black" onclick="getMyLocation('rider')">🛰️ شغل الـ GPS - حدد مكاني</button>
    <button class="btn-green" onclick="startLive('rider')">▶️ شغل تتبع لايف - عشان تشوف السواق بيتحرك</button>
    <h3>عايز تروح فين؟</h3>
    <input id="fromLoc" placeholder="من فين؟ مثال: شارع 15 سوهاج">
    <input id="toLoc" placeholder="رايح فين؟ مثال: موقف سوهاج">
    <button class="btn-orange" onclick="requestRide()">اطلب توكتوك الآن 🛺 - بمكاني GPS</button>
  </div>
  <div id="myRequests"></div>
  <div class="card"><h3>🛺 السواقين المتاحين حواليك</h3><div id="liveDrivers"></div></div>
</div>

<div id="driverScreen" class="hidden">
  <div class="top"><h3>أهلا يا كابتن <span id="driverName"></span> 🛺</h3><button style="width:auto" class="btn-gray" onclick="logout()">خروج</button></div>
  <div class="card" id="driverStatusCard"></div>
  <div id="myActiveRideBox"></div>
  <div class="card">
    <h3>🛰️ GPS السواق</h3>
    <div id="mapDriver"></div>
    <div class="gps-box" id="driverGpsBox">🛰️ دوس "شغل الـ GPS" عشان الراكب يشوفك</div>
    <button class="btn-black" onclick="getMyLocation('driver')">🛰️ شغل الـ GPS - حدد مكاني</button>
    <button class="btn-green" onclick="startLive('driver')">▶️ شغل GPS لايف + أنا متاح 🟢</button>
    <div class="flex"><button class="btn-green" onclick="setOnline(true)">أنا متاح 🟢</button><button class="btn-gray" onclick="setOnline(false)">مش متاح 🔴</button></div>
  </div>
  <h3>الطلبات القريبة منك</h3><div id="driverRequests"></div>
</div>

<div id="adminScreen" class="hidden">
  <div class="top"><h3>لوحة تحكم الأدمن 👑 <span id="adminNameShow"></span></h3><button style="width:auto" class="btn-gray" onclick="logout()">خروج</button></div>
  <div class="card">
    <p>أهلا بيك يا أدمن، انت اللي بتقبل السواقين او ترفضهم او تحذفهم ✅❌🗑️</p>
    <div class="flex">
      <button class="btn-orange" onclick="loadAdmin('pending')">مستني القبول ⏳</button>
      <button class="btn-gray" onclick="loadAdmin('approved')">المقبولين ✅</button>
      <button class="btn-gray" onclick="loadAdmin('all')">الكل</button>
    </div>
    <p>السواقين: <b id="adminCount">0</b> | الركاب: <b id="riderCount">0</b> | الطلبات: <b id="reqCount">0</b></p>
    <button class="btn-blue" onclick="loadAdmin(currentAdminFilter)">🔄 تحديث القايمة</button>
  </div>
  <div id="adminList"></div>
</div>

<script>
const firebaseConfig = {
  apiKey: "AIzaSyD7f6PTn5vEsEddCtmTdj3EDVGAnRjm-_A",
  authDomain: "toktokgo-65af3.firebaseapp.com",
  projectId: "toktokgo-65af3",
  storageBucket: "toktokgo-65af3.firebasestorage.app",
  messagingSenderId: "586951920995",
  appId: "1:586951920995:web:6995f3d5b9fded3e16d756",
  measurementId: "G-J5SKCYEL8E"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();
const auth = firebase.auth();
let currentUser = null;
let mapRider=null, mapDriver=null, markerRider=null, myMarker=null;
let driverMarkers = {};
let myLat=null, myLng=null, mySpeed=0, myAccuracy=0, watchId=null;
let acceptedDriverMarker=null, routeLine=null;
let riderMarkerOnDriverMap=null, driverRouteLine=null;
let currentAdminFilter='pending';

const ADMIN_PHONE = "01100536612";
const ADMIN_PASS = "1234";
const ADMIN_NAME = "أدمن سوهاج";

function saveLogin(user){ localStorage.setItem('toktok_user', JSON.stringify({phone:user.phone, password:user.password, type:user.type})); }
function getSavedLogin(){ try{ return JSON.parse(localStorage.getItem('toktok_user')); }catch(e){ return null; } }
function clearLogin(){ localStorage.removeItem('toktok_user'); }

async function autoLogin(){
  let saved = getSavedLogin(); if(!saved) return false;
  if(saved.type==='admin'){
    if(saved.phone===ADMIN_PHONE && saved.password===ADMIN_PASS){
      currentUser={phone:ADMIN_PHONE, name:ADMIN_NAME, type:'admin'};
      document.getElementById('adminNameShow').innerText = '- ' + ADMIN_NAME;
      showScreen('adminScreen'); loadAdmin('pending'); return true;
    }
    clearLogin(); return false;
  }
  let doc = await db.collection('users').doc(saved.phone).get();
  if(!doc.exists){ clearLogin(); alert('حسابك اتحذف من الأدمن ❌'); return false; }
  let u = doc.data();
  if(u.status==='deleted' || u.status==='blocked'){ clearLogin(); alert('حسابك اتحذف / اتحظر من الأدمن ❌'); return false; }
  if(u.password!==saved.password){ clearLogin(); return false; }
  currentUser={phone:saved.phone, ...u};
  if(u.type==='driver'){
    if(u.status!=='approved'){ clearLogin(); return false; }
    document.getElementById('driverName').innerText=u.name;
    showScreen('driverScreen'); loadDriverRequests(); updateDriverStatus(); listenMyActiveRide(); checkIfDeletedLive(); return true;
  } else {
    document.getElementById('riderName').innerText=u.name;
    showScreen('riderScreen'); loadMyRequests(); listenLiveDrivers(); listenMyAcceptedRide(); checkIfDeletedLive(); return true;
  }
}
function checkIfDeletedLive(){
  if(!currentUser || currentUser.type==='admin') return;
  db.collection('users').doc(currentUser.phone).onSnapshot(doc=>{
    if(!doc.exists){ clearLogin(); alert('الأدمن حذف حسابك ❌'); location.reload(); }
    let u = doc.data();
    if(u && (u.status==='deleted' || u.status==='blocked')){ clearLogin(); alert('الأدمن حظر حسابك ❌'); location.reload(); }
  });
}
function initMap(id){
  let m = L.map(id).setView([26.56, 31.70], 11);
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',{maxZoom:19}).addTo(m);
  L.marker([26.557,31.694]).addTo(m).bindPopup('سوهاج المدينة 🛺');
  return m;
}
window.onload = async ()=>{ await autoLogin(); };
function calcDist(lat1,lon1,lat2,lon2){
  let R=6371, dLat=(lat2-lat1)*Math.PI/180, dLon=(lon2-lon1)*Math.PI/180;
  let a=Math.sin(dLat/2)*Math.sin(dLat/2)+Math.cos(lat1*Math.PI/180)*Math.cos(lat2*Math.PI/180)*Math.sin(dLon/2)*Math.sin(dLon/2);
  return R*2*Math.atan2(Math.sqrt(a),Math.sqrt(1-a));
}
function tukIcon(){ return L.divIcon({className:'', html:'<div style="font-size:32px;text-align:center">🛺</div>', iconSize:[36,36], iconAnchor:[18,18]}); }
function meIcon(){ return L.divIcon({className:'', html:'<div style="font-size:32px;text-align:center">📍</div>', iconSize:[36,36], iconAnchor:[18,18]}); }
function riderIcon(){ return L.divIcon({className:'', html:'<div style="font-size:32px;text-align:center">🧍</div>', iconSize:[36,36], iconAnchor:[18,18]}); }
function navigateToRider(riderLat, riderLng){
  if(!riderLat || !riderLng) return alert('الراكب ده مش مشغل GPS');
  window.open(`https://www.google.com/maps/dir/?api=1&origin=${myLat||''},${myLng||''}&destination=${riderLat},${riderLng}&travelmode=driving`, '_blank');
}
function navigateWaze(riderLat, riderLng){
  if(!riderLat) return alert('الراكب مش مشغل GPS');
  window.open(`https://waze.com/ul?ll=${riderLat},${riderLng}&navigate=yes`, '_blank');
}
function showRiderOnMyMap(riderLat, riderLng, riderName){
  if(!riderLat || !mapDriver) return alert('الراكب مش مشغل GPS');
  if(riderMarkerOnDriverMap) mapDriver.removeLayer(riderMarkerOnDriverMap);
  if(driverRouteLine) mapDriver.removeLayer(driverRouteLine);
  riderMarkerOnDriverMap = L.marker([riderLat,riderLng], {icon: riderIcon()}).addTo(mapDriver).bindPopup('🧍 الراكب '+riderName+' هنا').openPopup();
  if(myLat){
    driverRouteLine = L.polyline([[myLat,myLng],[riderLat,riderLng]], {color:'#F97316', weight:5, dashArray:'10,10'}).addTo(mapDriver);
    mapDriver.fitBounds(L.latLngBounds([[myLat,myLng],[riderLat,riderLng]]), {padding:[50,50]});
  } else { mapDriver.setView([riderLat,riderLng], 15); }
}
function listenMyActiveRide(){
  if(!currentUser) return;
  db.collection('requests').where('driverPhone','==',currentUser.phone).where('status','==','accepted').onSnapshot(snap=>{
    let html='';
    snap.forEach(doc=>{
      let r=doc.data();
      let dist = (myLat && r.riderLat)? calcDist(myLat,myLng,r.riderLat,r.riderLng) : 0;
      let distText = dist<1? Math.round(dist*1000)+' متر' : dist.toFixed(1)+' كم';
      let mins = Math.max(1, Math.round(dist/0.4));
      html += `<div class="nav-box"><b>🛺 انت رايح للراكب دلوقتي</b><br><b>${r.riderName}</b> - ${r.riderPhone} - من ${r.from} إلى ${r.to}<br><b style="color:#DC2626">يبعد عنك: ${distText} - هتوصل بعد: ${mins} دقيقة</b><br><br><button class="btn-blue" onclick="navigateToRider(${r.riderLat}, ${r.riderLng})">🧭 امشي على مكان الراكب - جوجل ماب</button><button class="btn-purple" onclick="navigateWaze(${r.riderLat}, ${r.riderLng})">🚗 امشي بـ Waze</button><button class="btn-orange" onclick="showRiderOnMyMap(${r.riderLat}, ${r.riderLng}, '${r.riderName}')">🗺️ شوف مكانه على خريطتي هنا</button><a href="tel:${r.riderPhone}"><button class="btn-green">📞 اتصل بالراكب</button></a><button class="btn-black" onclick="arrivedToRider('${doc.id}')">✅ وصلت للراكب</button></div>`;
    });
    document.getElementById('myActiveRideBox').innerHTML = html;
  });
}
async function arrivedToRider(id){
  await db.collection('requests').doc(id).update({status:'arrived'});
  alert('تمام يا كابتن ✅ الراكب اتبلغ انك وصلت');
  loadDriverRequests();
}
function updateGPSBox(who, lat, lng, speed, acc){
  let html = `🛰️ <b>● GPS شغال لايف</b><br>مكانك: ${lat.toFixed(6)} , ${lng.toFixed(6)}<br>الدقة: ${Math.round(acc)} متر | السرعة: ${Math.round(speed*3.6)} كم/س`;
  if(who==='rider') document.getElementById('riderGpsBox').innerHTML = html;
  else document.getElementById('driverGpsBox').innerHTML = html;
}
function getMyLocation(who){
  if(!navigator.geolocation) return alert('موبايلك مش بيدعم GPS');
  navigator.geolocation.getCurrentPosition(pos=>{
    myLat = pos.coords.latitude; myLng = pos.coords.longitude; mySpeed = pos.coords.speed||0; myAccuracy = pos.coords.accuracy||0;
    updateGPSBox(who, myLat, myLng, mySpeed, myAccuracy);
    let map = who==='rider'? mapRider : mapDriver;
    if(map){
      map.setView([myLat,myLng], 15);
      if(myMarker) map.removeLayer(myMarker);
      myMarker = L.marker([myLat,myLng], {icon: meIcon()}).addTo(map).bindPopup('انت هنا 📍').openPopup();
    }
    if(who==='rider') document.getElementById('fromLoc').value = 'مكاني GPS - ' + myLat.toFixed(5) + ',' + myLng.toFixed(5);
    if(currentUser) db.collection('users').doc(currentUser.phone).update({lat:myLat, lng:myLng, speed:mySpeed, accuracy:myAccuracy, lastSeen: firebase.firestore.FieldValue.serverTimestamp()});
    alert('تم ✅ جبنا مكانك بالـ GPS');
  }, ()=> alert('شغل الـ GPS واسمح للتطبيق يشوف مكانك'), {enableHighAccuracy:true, timeout:15000, maximumAge:0});
}
function startLive(who){
  if(!navigator.geolocation) return alert('شغل الـ GPS');
  alert('تتبع GPS لايف اشتغل ✅');
  if(watchId) navigator.geolocation.clearWatch(watchId);
  watchId = navigator.geolocation.watchPosition(pos=>{
    myLat = pos.coords.latitude; myLng = pos.coords.longitude; mySpeed = pos.coords.speed||0; myAccuracy = pos.coords.accuracy||0;
    updateGPSBox(who, myLat, myLng, mySpeed, myAccuracy);
    let map = who==='rider'? mapRider : mapDriver;
    if(map){
      if(myMarker) map.removeLayer(myMarker);
      myMarker = L.marker([myLat,myLng], {icon: meIcon()}).addTo(map).bindPopup(who==='rider'?'انت هنا 📍':'انت هنا يا كابتن 🛺');
    }
    if(currentUser) db.collection('users').doc(currentUser.phone).update({lat:myLat, lng:myLng, speed:mySpeed, accuracy:myAccuracy, online: who==='driver'?true:undefined, lastSeen: firebase.firestore.FieldValue.serverTimestamp()});
  }, null, {enableHighAccuracy:true, maximumAge:3000, timeout:10000});
  if(who==='rider'){ listenLiveDrivers(); listenMyAcceptedRide(); }
  if(who==='driver' && currentUser){ db.collection('users').doc(currentUser.phone).update({online:true}); listenMyActiveRide(); }
}
function listenLiveDrivers(){
  db.collection('users').where('type','==','driver').where('online','==',true).onSnapshot(snap=>{
    let html='';
    snap.forEach(d=>{
      let u=d.data(); if(!u.lat) return;
      let dist = myLat? calcDist(myLat,myLng,u.lat,u.lng) : 0;
      html += `<div class="card">🛺 <b>${u.name}</b> - ${u.area} - <span class="live">● قدامك على الخريطة</span><br><small>توكتوك: ${u.tukNumber||'-'} - ${u.phone}<br>يبعد عنك: ${dist<1? Math.round(dist*1000)+' متر' : dist.toFixed(1)+' كم'}</small></div>`;
      if(mapRider){
        if(driverMarkers[d.id]) mapRider.removeLayer(driverMarkers[d.id]);
        driverMarkers[d.id] = L.marker([u.lat,u.lng], {icon: tukIcon()}).addTo(mapRider).bindPopup('🛺 كابتن '+u.name);
      }
    });
    document.getElementById('liveDrivers').innerHTML = html || '<p>مفيش سواقين متاحين دلوقتي</p>';
  });
}
function listenMyAcceptedRide(){
  if(!currentUser) return;
  db.collection('requests').where('riderPhone','==',currentUser.phone).where('status','==','accepted').onSnapshot(snap=>{
    snap.forEach(doc=>{
      let r = doc.data(); if(!r.driverPhone) return;
      db.collection('users').doc(r.driverPhone).onSnapshot(d=>{
        let u = d.data(); if(!u || !u.lat || !mapRider) return;
        if(acceptedDriverMarker) mapRider.removeLayer(acceptedDriverMarker);
        acceptedDriverMarker = L.marker([u.lat,u.lng], {icon: tukIcon()}).addTo(mapRider).bindPopup('🛺 سواقك '+u.name+' جايلك').openPopup();
        let dist = myLat? calcDist(myLat,myLng,u.lat,u.lng) : 0;
        let mins = Math.max(1, Math.round(dist / 0.4));
        let distText = dist<1? Math.round(dist*1000)+' متر' : dist.toFixed(1)+' كم';
        document.getElementById('driverComingBox').classList.remove('hidden');
        document.getElementById('driverComingBox').innerHTML = `<div class="driver-coming">🛺 <b>سواقك جايلك دلوقتي!</b><br>كابتن <b>${u.name}</b> - ${u.phone}<br><b style="font-size:22px;color:#DC2626">يبعد عنك: ${distText}</b><br><b>هيوصلك بعد حوالي: ${mins} دقيقة ⏰</b><br><a href="tel:${u.phone}">📞 اتصل بالسواق</a></div>`;
        updateRouteLine(u.lat, u.lng);
        if(myLat){ mapRider.fitBounds(L.latLngBounds([[myLat,myLng],[u.lat,u.lng]]), {padding:[50,50]}); }
      });
    });
  });
}
function updateRouteLine(dLat, dLng){
  if(!myLat || !dLat || !mapRider) return;
  if(routeLine) mapRider.removeLayer(routeLine);
  routeLine = L.polyline([[myLat,myLng],[dLat,dLng]], {color:'#DC2626', weight:4, dashArray:'10,10'}).addTo(mapRider);
}
function showTab(t){
  document.getElementById('loginTab').classList.toggle('hidden', t!=='login');
  document.getElementById('riderRegisterTab').classList.toggle('hidden', t!=='riderRegister');
  document.getElementById('driverRegisterTab').classList.toggle('hidden', t!=='driverRegister');
  document.getElementById('adminLoginTab').classList.toggle('hidden', t!=='adminLogin');
  ['login','riderRegister','driverRegister','adminLogin'].forEach(k=>{
    let b = document.getElementById('btn_'+k);
    if(b) b.classList.toggle('tab-active', k===t);
  });
}
function showScreen(id){
  ['authScreen','riderScreen','driverScreen','adminScreen'].forEach(s=>document.getElementById(s).classList.add('hidden'));
  document.getElementById(id).classList.remove('hidden');
  setTimeout(()=>{
    if(id==='riderScreen' && !mapRider){
      mapRider = initMap('map');
      mapRider.on('click', function(e){
        if(markerRider) mapRider.removeLayer(markerRider);
        markerRider = L.marker(e.latlng).addTo(mapRider).bindPopup('مكانك هنا 📍').openPopup();
        document.getElementById('fromLoc').value = 'مكاني على الخريطة - ' + e.latlng.lat.toFixed(4) + ',' + e.latlng.lng.toFixed(4);
        myLat=e.latlng.lat; myLng=e.latlng.lng;
      });
    }
    if(id==='driverScreen' && !mapDriver){ mapDriver = initMap('mapDriver'); }
    if(mapRider) mapRider.invalidateSize();
    if(mapDriver) mapDriver.invalidateSize();
  },300);
}
function logout(){ if(watchId) navigator.geolocation.clearWatch(watchId); clearLogin(); location.reload(); }

async function doAdminLogin(){
  let phone=document.getElementById('adminLoginPhone').value.trim();
  let pass=document.getElementById('adminLoginPass').value.trim();
  if(!phone||!pass) return alert('اكتب رقم الأدمن وكلمة السر');
  if(phone===ADMIN_PHONE && pass===ADMIN_PASS){
    currentUser={phone:ADMIN_PHONE, name:ADMIN_NAME, type:'admin'};
    saveLogin({phone:ADMIN_PHONE, password:ADMIN_PASS, type:'admin'});
    document.getElementById('adminNameShow').innerText = '- ' + ADMIN_NAME;
    showScreen('adminScreen'); loadAdmin('pending'); return;
  }
  alert('رقم الأدمن او كلمة السر غلط ❌');
}

async function doRiderRegister(){
  let name=document.getElementById('regNameRider').value.trim();
  let phone=document.getElementById('regPhoneRider').value.trim();
  let pass=document.getElementById('regPassRider').value.trim();
  if(!name) return alert('اكتب اسمك');
  if(phone.length!==11) return alert('رقم الموبايل لازم 11 رقم');
  if(!pass) pass = phone;
  try{
    let old = await db.collection('users').doc(phone).get();
    if(old.exists) return alert('الرقم ده متسجل قبل كده، ادخل من زرار دخول');
    await db.collection('users').doc(phone).set({ name, phone, password: pass, nationalId: '', type:'rider', gov:'سوهاج', area:'سوهاج المدينة', tukNumber:'', license:'', status:'approved', online:false, phoneVerified:false, lat:null, lng:null, speed:0, createdAt: firebase.firestore.FieldValue.serverTimestamp() });
    saveLogin({phone, password:pass, type:'rider'});
    alert('تم تسجيل الراكب يا '+name+' ✅');
    location.reload();
  }catch(e){ alert('مشكلة في التسجيل: '+e.message); }
}
async function doDriverRegister(){
  let name=document.getElementById('regName').value.trim();
  let phone=document.getElementById('regPhone').value.trim();
  let pass=document.getElementById('regPass').value.trim();
  let national=document.getElementById('regNational').value.trim();
  let gov=document.getElementById('regGov').value;
  let area=document.getElementById('regArea').value;
  let tuk=document.getElementById('regTuk').value.trim();
  if(!name||!phone||!pass) return alert('كمل اسم السواق ورقمه وكلمة السر');
  if(phone.length!==11) return alert('رقم الموبايل لازم 11 رقم');
  if(!tuk) return alert('اكتب رقم التوكتوك');
  try{
    let old = await db.collection('users').doc(phone).get();
    if(old.exists) return alert('الرقم ده متسجل قبل كده');
    await db.collection('users').doc(phone).set({ name, phone, password:pass, nationalId:national, type:'driver', gov, area, tukNumber: tuk, license: document.getElementById('regLicense').value.trim(), status:'pending', online:false, phoneVerified:false, lat:null, lng:null, speed:0, createdAt: firebase.firestore.FieldValue.serverTimestamp() });
    saveLogin({phone, password:pass, type:'driver'});
    alert('تم تسجيل السواق يا كابتن ✅ مستني الأدمن يقبلك'); location.reload();
  }catch(e){ alert('مشكلة في التسجيل: '+e.message); }
}
async function doLogin(){
  let phone=document.getElementById('loginPhone').value.trim();
  let pass=document.getElementById('loginPass').value.trim();
  if(!phone) return alert('اكتب رقمك');
  if(!pass) pass = phone;
  let doc = await db.collection('users').doc(phone).get();
  if(!doc.exists) return alert('الرقم ده مش مسجل، اعمل تسجيل جديد');
  let u = doc.data();
  if(u.status==='deleted' || u.status==='blocked') return alert('حسابك اتحذف / اتحظر من الأدمن ❌');
  if(u.type==='rider'){
    if(pass!==phone && pass!==u.password) return alert('كلمة السر غلط');
    currentUser={phone, ...u};
    saveLogin({phone, password:u.password, type:'rider'});
    document.getElementById('riderName').innerText=u.name;
    showScreen('riderScreen'); loadMyRequests(); listenLiveDrivers(); listenMyAcceptedRide(); checkIfDeletedLive(); return;
  }
  if(u.password!==pass) return alert('كلمة السر غلط');
  currentUser={phone, ...u};
  saveLogin({phone, password:pass, type:'driver'});
  if(u.type==='driver'){ if(u.status!=='approved'){ alert('حسابك لسه مستني القبول من الأدمن ⏳'); return; } document.getElementById('driverName').innerText=u.name; showScreen('driverScreen'); loadDriverRequests(); updateDriverStatus(); listenMyActiveRide(); checkIfDeletedLive(); }
}
async function requestRide(){
  let from=document.getElementById('fromLoc').value.trim();
  let to=document.getElementById('toLoc').value.trim();
  if(!from||!to) return alert('اكتب من فين ورايح فين');
  if(!myLat){ let ok = confirm('الـ GPS لسه مش شغال، عايز تشغله الأول؟'); if(ok){ getMyLocation('rider'); return; } }
  await db.collection('requests').add({ riderPhone:currentUser.phone, riderName:currentUser.name, from, to, area:currentUser.area, gov:currentUser.gov, riderLat: myLat, riderLng: myLng, status:'waiting', createdAt: firebase.firestore.FieldValue.serverTimestamp() });
  alert('تم ارسال طلبك 🛺'); loadMyRequests();
}
async function loadMyRequests(){
  let snap=await db.collection('requests').where('riderPhone','==',currentUser.phone).orderBy('createdAt','desc').limit(5).get();
  let html=''; snap.forEach(d=>{ let r=d.data(); html+=`<div class="card">من ${r.from} إلى ${r.to} - <b>${r.status==='waiting'?'مستني سواق ⏳':r.status==='accepted'?'سواقك جايلك 🛺':r.status==='arrived'?'السواق وصل ✅':'تم ✅'}</b></div>`; });
  document.getElementById('myRequests').innerHTML=html||'<p style="text-align:center;color:#888">لسه مفيش طلبات</p>';
}
async function setOnline(v){
  await db.collection('users').doc(currentUser.phone).update({online:v});
  if(v) startLive('driver'); else if(watchId) navigator.geolocation.clearWatch(watchId);
  alert(v?'انت دلوقتي متاح 🟢':'انت مش متاح 🔴'); loadDriverRequests();
}
async function updateDriverStatus(){
  let d=await db.collection('users').doc(currentUser.phone).get();
  if(!d.exists){ clearLogin(); alert('حسابك اتحذف من الأدمن'); location.reload(); return; }
  document.getElementById('driverStatusCard').innerHTML=`حالتك: ${d.data().online?'متاح 🟢':'مش متاح 🔴'} | منطقتك: ${d.data().area}`;
}
async function loadDriverRequests(){
  let snap=await db.collection('requests').where('status','==','waiting').orderBy('createdAt','desc').limit(10).get();
  let html=''; snap.forEach(doc=>{ let r=doc.data(); let dist = (myLat && r.riderLat)? calcDist(myLat,myLng,r.riderLat,r.riderLng) : 0; let distText = dist? (dist<1? Math.round(dist*1000)+' متر' : dist.toFixed(1)+' كم') : 'من غير GPS'; html+=`<div class="card"><b>${r.riderName}</b> - ${r.riderPhone} - من ${r.from} إلى ${r.to}<br><small>${r.area} - 📍 يبعد عنك: ${distText}</small><br><button class="btn-blue" onclick="navigateToRider(${r.riderLat}, ${r.riderLng})">🧭 امشي على مكانه - جوجل ماب</button><button class="btn-purple" onclick="navigateWaze(${r.riderLat}, ${r.riderLng})">🚗 امشي بـ Waze</button><button class="btn-orange" onclick="showRiderOnMyMap(${r.riderLat}, ${r.riderLng}, '${r.riderName}')">🗺️ شوف مكانه على خريطتي</button><button class="btn-green" onclick="acceptRide('${doc.id}')">اقبل الطلب ✅</button></div>`; });
  document.getElementById('driverRequests').innerHTML=html||'<p style="text-align:center;color:#888">مفيش طلبات دلوقتي</p>';
}
async function acceptRide(id){
  await db.collection('requests').doc(id).update({status:'accepted', driverPhone:currentUser.phone, driverName:currentUser.name});
  alert('قبلت الطلب ✅'); loadDriverRequests(); listenMyActiveRide();
}

// ===== اصلاح ازرار الادمن - بيستخدم ID مش الرقم =====
async function loadAdmin(filter='pending'){
  currentAdminFilter = filter;
  try{
    let snap=await db.collection('users').get(); let drivers=[], riders=[];
    snap.forEach(d=>{ let u=d.data(); let item={id:d.id, ...u}; if(!item.phone) item.phone = d.id; if(!item.name) item.name = 'من غير اسم'; if(!item.area) item.area=''; if(u.type==='driver') drivers.push(item); else riders.push(item); });
    document.getElementById('adminCount').innerText=drivers.length; document.getElementById('riderCount').innerText=riders.length;
    let reqSnap=await db.collection('requests').get(); document.getElementById('reqCount').innerText=reqSnap.size;
    let list = filter==='all'? [...drivers,...riders] : filter==='approved'? drivers.filter(d=>d.status==='approved') : drivers.filter(d=>d.status==='pending');
    let html=''; list.forEach(u=>{
      html+=`<div class="card"><b>${u.name}</b> - ${u.phone} <span class="badge ${u.status}">${u.status==='pending'?'مستني ⏳':u.status==='approved'?'مقبول ✅':'مرفوض ❌'}</span><br><small>${u.type==='driver'?'سواق 🛺':'راكب'} - ${u.area} - توكتوك: ${u.tukNumber||'-'}</small><br>
      <div class="flex"><button class="btn-green" onclick="setStatus('${u.id}','approved')">قبول ✅</button><button class="btn-red" onclick="setStatus('${u.id}','rejected')">رفض ❌</button></div>
      <button class="btn-black" onclick="deleteUser('${u.id}','${u.name}')">🗑️ حذف نهائي</button>
      <button class="btn-gray" onclick="blockUser('${u.id}','${u.name}')">🚫 حظر</button></div>`;
    });
    document.getElementById('adminList').innerHTML=html||'<p style="text-align:center">مفيش حد هنا</p>';
  }catch(e){ alert('مشكلة في تحميل الادمن: '+e.message); }
}
async function setStatus(docId,status){
  try{
    await db.collection('users').doc(docId).update({status: status});
    alert(status==='approved' ? 'تم قبول السواق ✅' : 'تم رفض السواق ❌');
    await loadAdmin(currentAdminFilter);
  }catch(e){ alert('مشكلة: '+e.message); }
}
async function deleteUser(docId, name){
  if(!confirm('متأكد تحذف ' + name + ' نهائي؟')) return;
  try{
    await db.collection('users').doc(docId).delete();
    alert('تم حذف ' + name + ' 🗑️');
    await loadAdmin(currentAdminFilter);
  }catch(e){ alert('مشكلة في الحذف: '+e.message); }
}
async function blockUser(docId, name){
  if(!confirm('تحظر ' + name + ' ؟')) return;
  try{
    await db.collection('users').doc(docId).update({status:'blocked'});
    alert('تم حظر ' + name + ' 🚫');
    await loadAdmin(currentAdminFilter);
  }catch(e){ alert('مشكلة في الحظر: '+e.message); }
}
</script>
</body>
</html>
