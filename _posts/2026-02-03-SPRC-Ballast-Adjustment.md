---
layout: post
title: "SPRC Ballast Adjustment"
date: 2026-02-03
---

<body>
    <p>Cell highlighted in yellow for user input</p>
    <p>Current Turntable Rotation Angle (Counter-Clockwise):<input type="number" id="rotation" name="rotation" value="254" style="background-color: BlanchedAlmond;"></p>
    <table>
        <tr>
            <th>Turntable original counter weight location</th>
            <th>X(mm)</th>
            <th>Y(mm)</th>
        </tr>
        <tr>
            <td>T1 (Max 21.2MT)</td>
            <td id="T1x">6778</td>
            <td id="T1y">2317</td>
        </tr>
        <tr>
            <td>T2 (Max 12.9MT)</td>
            <td id="T2x">7149</td>
            <td id="T2y">453</td>
        </tr>
    </table>
    <img src="/assets/images/SPRC_Draft_Location.png" alt="SPRC_Draft_Location">
    <table>
        <tr>
            <th>Draft Mark</th>
            <th>Readings(MT)</th>
        </tr>
        <tr>
            <td>A  </td>
            <td><input type="number" id="R1" name="R1" value="2206" style="background-color: BlanchedAlmond;"></td>
        </tr>
        <tr>
            <td>B</td>
            <td><input type="number" id="R2" name="R2" value="1994" style="background-color: BlanchedAlmond;"></td>
        </tr>
        <tr>
            <td>C</td>
            <td><input type="number" id="R3" name="R3" value="2194" style="background-color: BlanchedAlmond;"></td>
        </tr>
        <tr>
            <td>D</td>
            <td><input type="number" id="R4" name="R4" value="2406" style="background-color: BlanchedAlmond;"></td>
        </tr>
    </table>
    <button type="button" onclick="calculate()">Calculate</button>
    <p></p>
    <p>Results listed below</p>
    <table>
        <tr>
            <th>Current turntable counter weight location</th>
            <th>X(mm)</th>
            <th>Y(mm)</th>
        </tr>
        <tr>
            <td>T1 (Max 21.2MT)</td>
            <td id="T1xt">0</td>
            <td id="T1yt">0</td>
        </tr>
        <tr>
            <td>T2 (Max 12.9MT)</td>
            <td id="T2xt">0</td>
            <td id="T2yt">0</td>
        </tr>
    </table>
    <table>
        <tr>
            <td>Total Weight (MT)</td>
            <td id="weight" style="width: 150px;"></td>
        </tr>
        <tr>
            <td>Heeling Moment X (MT.m)</td>
            <td id="resultMomentX"></td>
        </tr>
        <tr>
            <td>Heeling Moment Y (MT.m)</td>
            <td id="resultMomentY"></td>
        </tr>
        <tr>
            <td>Heeling Moment (MT.m)</td>
            <td id="resultMoment"></td>
        </tr>
    </table>
    <table>
        <tr>
            <th>Ballast Adjustment</th>
            <th>Weight (MT)</th>
        </tr>
        <tr>
            <td>T1</td>
            <td id="T1Adjust"></td>
        </tr>
        <tr>
            <td>T2</td>
            <td id="T2Adjust"></td>
        </tr>
    </table>
    <script>
        function calculate() {
            const rotation = Number(document.getElementById("rotation").value)
            var T1x = Number(document.getElementById("T1x").textContent)
            var T1y = Number(document.getElementById("T1y").textContent)
            var T2x = Number(document.getElementById("T2x").textContent)
            var T2y = Number(document.getElementById("T2y").textContent)
            const R1 = Number(document.getElementById("R1").value)
            const R2 = Number(document.getElementById("R2").value)
            const R3 = Number(document.getElementById("R3").value)
            const R4 = Number(document.getElementById("R4").value)
            let T1x2 = T1x;
            let T1y2 = T1y;
            let T2x2 = T2x;
            let T2y2 = T2y;
            const TPM = 106.51;
            const L = 12.02;
            const rightingMoment = 5.2;
            T1x = Math.sqrt(T1x2**2+T1y2**2)*Math.cos(Math.atan2(T1y2,T1x2)+rotation*Math.PI/180);
            T1y = Math.sqrt(T1x2**2+T1y2**2)*Math.sin(Math.atan2(T1y2,T1x2)+rotation*Math.PI/180);
            T2x = Math.sqrt(T2x2**2+T2y2**2)*Math.cos(Math.atan2(T2y2,T2x2)+rotation*Math.PI/180);
            T2y = Math.sqrt(T2x2**2+T2y2**2)*Math.sin(Math.atan2(T2y2,T2x2)+rotation*Math.PI/180);
            document.getElementById("T1xt").innerHTML = T1x.toFixed(0);
            document.getElementById("T1yt").innerHTML = T1y.toFixed(0);
            document.getElementById("T2xt").innerHTML = T2x.toFixed(0);
            document.getElementById("T2yt").innerHTML = T2y.toFixed(0);
            let averageDraft = (R1+R2+R3+R4)/4;
            let weight = TPM*averageDraft/1000;
            let heelX = R1 - R3;
            let heelY = R2 - R4;
            let degX = Math.atan(heelX/L/1000)*180/Math.PI;
            let degY = Math.atan(heelY/L/1000)*180/Math.PI;
            let resultMomentX = degX*rightingMoment;
            let resultMomentY = degY*rightingMoment;
            let resultMoment = Math.sqrt(resultMomentX**2 + resultMomentY**2);
            let T2Adjust = ((-resultMomentY*1000/weight+resultMomentX*1000/weight*T1y/T1x)/(-T1y*T2x/T1x+T2y))*weight;
            let T1Adjust = ((-resultMomentX*1000/weight-T2Adjust/weight*T2x)/T1x)*weight;
            document.getElementById("weight").innerHTML = weight.toFixed(3);
            document.getElementById("resultMoment").innerHTML = resultMoment.toFixed(3);
            document.getElementById("resultMomentX").innerHTML = resultMomentX.toFixed(3);
            document.getElementById("resultMomentY").innerHTML = resultMomentY.toFixed(3);
            document.getElementById("T2Adjust").innerHTML = T2Adjust.toFixed(3);
            document.getElementById("T1Adjust").innerHTML = T1Adjust.toFixed(3);
        }
    </script>
</body>
