<!DOCTYPE html>
<html>
<head>
<title>Maharishi Tour & Travels | Tempo Traveller Fare</title>

<style>
body{font-family:Arial;background:#f2f2f2;}
.box{
 max-width:540px;margin:20px auto;background:#fff;
 padding:20px;border-radius:12px;
 box-shadow:0 0 12px rgba(0,0,0,0.15);
}
h2{text-align:center;margin-bottom:5px;}
.brand{text-align:center;color:#555;font-size:14px;margin-bottom:15px;}
input,select,button{
 width:100%;padding:9px;margin-top:8px;margin-bottom:12px;
}
button{background:#28a745;color:#fff;border:none;font-size:16px;border-radius:6px;}
.whatsapp{background:#25D366;}
.result{font-weight:bold;text-align:center;font-size:16px;}
.footer{text-align:center;font-size:12px;color:#777;margin-top:10px;}
.hide{display:none;}
</style>
</head>

<body>

<div class="box">
<h2>Tempo Traveller Fare Calculator</h2>
<div class="brand">Maharishi Tour & Travels</div>

<input id="pickup" placeholder="Pickup Location">
<input id="d1" placeholder="Destination 1">
<input id="d2" placeholder="Destination 2 (optional)">
<input id="d3" placeholder="Destination 3 (optional)">

<select id="fareType" onchange="toggleDays()">
 <option value="km">Per KM Basis</option>
 <option value="day">Per Day Basis (₹9000/day)</option>
</select>

<select id="vehicle">
 <option value="35">Tempo Traveller 26 Seater (₹35/km)</option>
 <option value="30">Tempo Traveller 22 Seater (₹30/km)</option>
 <option value="28">Tempo Traveller 20 Seater (₹28/km)</option>
 <option value="25">Tempo Traveller 17 Seater (₹25/km)</option>
</select>

<div id="daysBox" class="hide">
 <input id="days" type="number" placeholder="Number of Days">
</div>

<input id="night" type="number" placeholder="Night Halt Charges (optional)">
<input id="toll" type="number" placeholder="Toll Charges (estimated)">

<button onclick="calculate()">Calculate Fare</button>
<button class="whatsapp" onclick="book()">Book on WhatsApp</button>

<div class="result" id="output"></div>
<div class="footer">Final fare subject to route & toll confirmation</div>
</div>

<script>
let totalKM=0, finalFare=0;

function toggleDays(){
 daysBox.style.display = fareType.value==="day"?"block":"none";
}

function dist(a,b,cb){
 let s=new google.maps.DistanceMatrixService();
 s.getDistanceMatrix({
  origins:[a],destinations:[b],
  travelMode:'DRIVING',unitSystem:google.maps.UnitSystem.METRIC
 },(r,st)=>{
  if(st==='OK'){cb(r.rows[0].elements[0].distance.value/1000);}
 });
}

function calculate(){
 if(!pickup.value||!d1.value){
  alert("Enter pickup & destination");return;
 }
 totalKM=0;

 dist(pickup.value,d1.value,km1=>{
  totalKM+=km1;
  if(d2.value){
   dist(d1.value,d2.value,km2=>{
    totalKM+=km2;
    if(d3.value){
     dist(d2.value,d3.value,km3=>{
      totalKM+=km3;process();
     });
    } else process();
   });
  } else process();
 });
}

function process(){
 let rate=Number(vehicle.value);
 let days=Number(days.value||1);
 let night=Number(night.value||0);
 let toll=Number(toll.value||0);
 let driver=days*500;

 if(fareType.value==="day"){
  finalFare=(days*9000)+driver+night+toll;
 } else {
  finalFare=(totalKM*rate)+driver+night+toll;
 }

 output.innerHTML=
  "Total Distance: "+totalKM.toFixed(1)+" KM<br>"+
  "Driver Allowance: ₹"+driver+"<br>"+
  "Estimated Fare: ₹"+finalFare.toFixed(0);
}

function book(){
 if(finalFare===0){alert("Calculate fare first");return;}
 let msg=
 "Maharishi Tour & Travels%0A"+
 "Pickup: "+pickup.value+"%0A"+
 "Route: "+d1.value+" "+d2.value+" "+d3.value+"%0A"+
 "Distance: "+totalKM.toFixed(1)+" KM%0A"+
 "Fare: ₹"+finalFare;
 window.open("https://wa.me/919467675902?text="+msg);
}
</script>

<script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places"></script>
<script>
["pickup","d1","d2","d3"].forEach(id=>{
 new google.maps.places.Autocomplete(document.getElementById(id));
});
</script>

</body>
</html>
