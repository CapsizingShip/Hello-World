---
layout: post
title: "Par Hawaii Ballast Adjustment - Floating Condition"
date: 2026-07-28
---

<body>
    <p>Cell highlighted in yellow for user input</p>
    <img src="/Hello-World/assets/images/ParHawaii_BallastArrangement.png" alt="ParHawaii_Arrangement">
    <p>Current Turntable Rotation Angle (Counter-Clockwise) (eg. 10 deg of TT x-axis counter-clockwise from Buoy x-axis): <br> <input type="number" id="rotation" name="rotation" value="10" style="background-color: BlanchedAlmond;"></p>
    <table>
        <tr>
            <th>Turntable original counter weight location</th>
            <th>X(mm)</th>
            <th>Y(mm)</th>
        </tr>
        <tr>
            <td>T1 (Max 25MT)</td>
            <td id="T1x">0</td>
            <td id="T1y">6916</td>
        </tr>
        <tr>
            <td>T2 (Max 11MT)</td>
            <td id="T2x">6734.1</td>
            <td id="T2y">1064.1</td>
        </tr>
        <tr>
            <td>T3 (Max 11MT)</td>
            <td id="T3x">6734.1</td>
            <td id="T3y">-1064.1</td>
        </tr>
    </table>
    <table>
        <tr>
            <th>Draft Mark</th>
            <th>Readings(MT)</th>
        </tr>
        <tr>
            <td>A (0 deg)  </td>
            <td><input type="number" id="R1" name="R1" value="3375" style="background-color: BlanchedAlmond;"></td>
        </tr>
        <tr>
            <td>B (60 deg)</td>
            <td><input type="number" id="R2" name="R2" value="3390" style="background-color: BlanchedAlmond;"></td>
        </tr>
        <tr>
            <td>C (120 deg)</td>
            <td><input type="number" id="R3" name="R3" value="3348" style="background-color: BlanchedAlmond;"></td>
        </tr>
        <tr>
            <td>D (180 deg)</td>
            <td><input type="number" id="R4" name="R4" value="3291" style="background-color: BlanchedAlmond;"></td>
        </tr>
        <tr>
            <td>E (240 deg)</td>
            <td><input type="number" id="R5" name="R5" value="3276" style="background-color: BlanchedAlmond;"></td>
        </tr>
        <tr>
            <td>F (300 deg)</td>
            <td><input type="number" id="R6" name="R6" value="3318" style="background-color: BlanchedAlmond;"></td>
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
            <td>T1 (Max 25MT)</td>
            <td id="T1xt">0</td>
            <td id="T1yt">0</td>
        </tr>
        <tr>
            <td>T2 (Max 11MT)</td>
            <td id="T2xt">0</td>
            <td id="T2yt">0</td>
        </tr>
        <tr>
            <td>T3 (Max 11MT)</td>
            <td id="T3xt">0</td>
            <td id="T3yt">0</td>
        </tr>
    </table>
    <table>
        <tr>
            <td>Average Draft (mm)</td>
            <td id="averageDraft" style="width: 150px;"></td>
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
        <tr>
            <td>T3</td>
            <td id="T3Adjust"></td>
        </tr>
    </table>
    <p>Positive ballast adjustment to add ballast weight, negative ballast adjustment to remove ballast weight.</p>
    <script>
        function calculate() {
            const rotation = Number(document.getElementById("rotation").value)
            var T1x = Number(document.getElementById("T1x").textContent)
            var T1y = Number(document.getElementById("T1y").textContent)
            var T2x = Number(document.getElementById("T2x").textContent)
            var T2y = Number(document.getElementById("T2y").textContent)
            var T3x = Number(document.getElementById("T3x").textContent)
            var T3y = Number(document.getElementById("T3y").textContent)
            const R1 = Number(document.getElementById("R1").value)
            const R2 = Number(document.getElementById("R2").value)
            const R3 = Number(document.getElementById("R3").value)
            const R4 = Number(document.getElementById("R4").value)
            const R5 = Number(document.getElementById("R5").value)
            const R6 = Number(document.getElementById("R6").value)
            let T1x2 = T1x;
            let T1y2 = T1y;
            let T2x2 = T2x;
            let T2y2 = T2y;
            let T3x2 = T3x;
            let T3y2 = T3y;
            const TPM = 95.93;
            const L = 11.52;
            const rightingMoment = 1.56;
            T1x = Math.sqrt(T1x2**2+T1y2**2)*Math.cos(Math.atan2(T1y2,T1x2)+rotation*Math.PI/180);
            T1y = Math.sqrt(T1x2**2+T1y2**2)*Math.sin(Math.atan2(T1y2,T1x2)+rotation*Math.PI/180);
            T2x = Math.sqrt(T2x2**2+T2y2**2)*Math.cos(Math.atan2(T2y2,T2x2)+rotation*Math.PI/180);
            T2y = Math.sqrt(T2x2**2+T2y2**2)*Math.sin(Math.atan2(T2y2,T2x2)+rotation*Math.PI/180);
            T3x = Math.sqrt(T3x2**2+T3y2**2)*Math.cos(Math.atan2(T3y2,T3x2)+rotation*Math.PI/180);
            T3y = Math.sqrt(T3x2**2+T3y2**2)*Math.sin(Math.atan2(T3y2,T3x2)+rotation*Math.PI/180);
            document.getElementById("T1xt").innerHTML = T1x.toFixed(0);
            document.getElementById("T1yt").innerHTML = T1y.toFixed(0);
            document.getElementById("T2xt").innerHTML = T2x.toFixed(0);
            document.getElementById("T2yt").innerHTML = T2y.toFixed(0);
            document.getElementById("T3xt").innerHTML = T3x.toFixed(0);
            document.getElementById("T3yt").innerHTML = T3y.toFixed(0);
            let averageDraft = (R1+R2+R3+R4+R5+R6)/6;
            let weight = TPM*averageDraft/1000;
            let heelX = R1 - R4;
            let heelY = (R5 + R6) / 2 - (R2 + R3) / 2;
            let degX = Math.atan(heelX/L/1000)*180/Math.PI;
            let degY = Math.atan(heelY/L/1000)*180/Math.PI;
            let resultMomentX = degX*rightingMoment;
            let resultMomentY = degY*rightingMoment;
            let resultMoment = Math.sqrt(resultMomentX**2 + resultMomentY**2);
            let T2Adjust = ((-resultMomentY*1000/weight+resultMomentX*1000/weight*T1y/T1x)/(-T1y*(T2x+T3x)/2/T1x+(T2y+T3y)/2))*weight/2;
            let T3Adjust = T2Adjust;
            let T1Adjust = ((-resultMomentX*1000/weight-T2Adjust/weight*T2x-T3Adjust/weight*T3x)/T1x)*weight;
            document.getElementById("averageDraft").innerHTML = averageDraft.toFixed(3);
            document.getElementById("resultMoment").innerHTML = resultMoment.toFixed(3);
            document.getElementById("resultMomentX").innerHTML = resultMomentX.toFixed(3);
            document.getElementById("resultMomentY").innerHTML = resultMomentY.toFixed(3);
            document.getElementById("T2Adjust").innerHTML = T2Adjust.toFixed(3);
            document.getElementById("T3Adjust").innerHTML = T3Adjust.toFixed(3);
            document.getElementById("T1Adjust").innerHTML = T1Adjust.toFixed(3);
        }
    </script>
</body>
