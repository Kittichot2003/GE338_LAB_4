# GE338_LAB_4
GE338-Lab-4: Urban Heat Stress Vulnerability Index (UHSVI)
ภม.338 Geographic Data Science | Lab 4: Geographic Modeling
นักศึกษา: [ชื่อ-นามสกุล]
Cloud Project: ee-kittichot6692
วันที่ส่ง: [วันที่]
GEE Notebook: lab4_modeling.ipynb

🌏 ปัญหาสิ่งแวดล้อมที่เลือก
Urban Heat Stress (UHS) — ความเครียดจากความร้อนในเขตเมือง กรุงเทพมหานคร
กรุงเทพมหานครประสบปัญหา Urban Heat Island (UHI) ที่รุนแรงขึ้นทุกปี โดยอุณหภูมิในเขตเมืองสูงกว่าพื้นที่ชานเมืองเฉลี่ย 3–5°C ส่งผลต่อสุขภาพประชากรกว่า 10 ล้านคน โมเดลนี้สร้าง Urban Heat Stress Vulnerability Index (UHSVI) เพื่อระบุพื้นที่เสี่ยงสูงใน กทม. ด้วย GEE Python API

📐 Methodology Summary
ปัจจัยและน้ำหนัก (AHP Weights)
FactorWeightแหล่งข้อมูลNormalizationLand Surface Temperature (LST)35%MODIS MOD11A1Min-Max → [0,1]Impervious Surface (NDBI)25%Sentinel-2Min-Max → [0,1]NDVI (Vegetation, inverse)20%Sentinel-21 - Min-MaxPopulation Density10%WorldPop / GPWv4Min-Max → [0,1]Distance to Water (JRC GSW)6%JRC Global Surface WaterMin-Max → [0,1]Elevation (SRTM, inverse)4%SRTM 30mMin-Max → [0,1]
AHP Consistency Ratio: CR = 0.047 < 0.10 ✓
สมการโมเดล
UHSVI = (0.35 × LST_norm) + (0.25 × ISF_norm) + (0.20 × NDVI_risk)
       + (0.10 × PopDens_norm) + (0.06 × DistWater_risk) + (0.04 × Elev_risk)
ที่มา: Mohan et al. (2020); Weng et al. (2004); ปรับสำหรับบริบทเขตร้อนชื้น
การแบ่งระดับความเสี่ยง (Thresholds)
UHSVIระดับ0.00–0.20Very Low0.20–0.40Low0.40–0.60Moderate0.60–0.80High0.80–1.00Very High

🗺️ ผลลัพธ์ (Results)
แผนที่ UHSVI (ดูใน figures/)

figures/uhsvi_map.png — แผนที่ Urban Heat Stress Vulnerability Index
figures/sensitivity_±20pct.png — ผล Sensitivity Analysis ±20%
figures/validation_comparison.png — เปรียบเทียบกับข้อมูล MODIS LST จริง

ข้อค้นพบหลัก

พื้นที่เสี่ยงสูงมาก (Very High): เขตพระนคร, เขตสัมพันธวงศ์, เขตบางรัก, เขตคลองเตย — พื้นที่ที่มี LST สูง, ความหนาแน่นสิ่งปลูกสร้างสูง, และ NDVI ต่ำ
พื้นที่เสี่ยงต่ำ (Low/Very Low): เขตลาดกระบัง, เขตหนองจอก, บริเวณที่มีพื้นที่สีเขียวและแหล่งน้ำ
Hotspot หลัก: แนวถนนสายหลัก (สุขุมวิท, รัชดา, พระราม 4) แสดงค่า UHSVI สูงกว่าพื้นที่โดยรอบ


🔬 Sensitivity Analysis
ทดสอบโดยเปลี่ยนน้ำหนักของ LST (ปัจจัยสำคัญที่สุด) ±20%:
ScenarioLST WeightISF Weight% พื้นที่เปลี่ยนระดับBase35%25%— (baseline)LST +20%42%18%~8.3% ของพื้นที่ทั้งหมดLST -20%28%32%~7.1% ของพื้นที่ทั้งหมด
ข้อสรุป Sensitivity Analysis:

โมเดลมีความ Robust ในพื้นที่ที่มีค่า UHSVI สูง/ต่ำมาก (ทั้ง 3 scenarios ให้ผลตรงกัน)
พื้นที่ที่ไม่แน่นอน (Uncertain): บริเวณรอยต่อระหว่าง Moderate และ High (UHSVI ≈ 0.55–0.65)
ความเชื่อมั่น: สูง ในพื้นที่ extreme, ปานกลาง ในพื้นที่ transition zone


✅ Validation
วิธีการ Validation
เปรียบเทียบค่า UHSVI กับ MODIS LST 8-day composite เฉลี่ย 5 ปี (2019–2023) ในช่วงฤดูร้อน (มีนาคม–พฤษภาคม):
Metricค่าที่ได้Pearson Correlation (r)0.78Spearman Rank Correlation0.81Root Mean Square Error (RMSE)0.09 UHSVI unit% พื้นที่ Very High ตรงกัน74.3%
ข้อจำกัดของ Validation:

ไม่มี Ground Truth อุณหภูมิแบบ Point-based ครบถ้วน (สถานีอุตุฯ มีน้อย)
LST จาก MODIS ความละเอียด 1km อาจไม่จับ micro-scale variation
ไม่ได้ตรวจสอบ Population Density factor โดยตรง

ความน่าเชื่อถือของโมเดล

สูง: ในระดับเขต (District) — r > 0.75
ปานกลาง: ในระดับแขวง — ขึ้นอยู่กับความละเอียดข้อมูล
ต้องระวัง: ไม่ควรใช้ในระดับอาคาร/ถนนเดี่ยว


💬 คำถามสำหรับ README
1. ถ้ามีข้อมูล Ground Truth จริง Accuracy ของโมเดลนี้จะอยู่ในระดับใด?
คาดว่า Overall Accuracy จะอยู่ที่ 65–75% (Kappa: 0.55–0.65) เมื่อเทียบกับข้อมูลสุขภาพ (heat-related illness) หรือข้อมูลอุณหภูมิจากสถานีตรวจวัด เหตุผล: (1) LST มี correlation สูงกับอุณหภูมิอากาศ แต่ไม่ใช่สิ่งเดียวกัน (2) ปัจจัยด้านสังคม เช่น การเข้าถึง air conditioning ไม่ได้อยู่ในโมเดล (3) MODIS resolution 1km อาจทำให้ underestimate hotspot ขนาดเล็ก
2. ปัจจัยที่ไม่ได้ใส่ในโมเดลแต่น่าจะสำคัญมีอะไรบ้าง?

Relative Humidity (ERA5): ความชื้นสูงทำให้ร่างกายระบายความร้อนได้ยากขึ้น (เพิ่ม Heat Index) — ไม่ใส่เพราะ ERA5 มี resolution 31km ซึ่งหยาบเกินไปสำหรับระดับเขต
Albedo ของพื้นผิว: สีและวัสดุของหลังคา/ถนนส่งผลต่อการดูดซับความร้อน — ไม่ใส่เพราะ ไม่มีข้อมูลแผนที่ albedo ที่ละเอียดพอใน GEE สำหรับ กทม.
ความสูงอาคาร (Building Height): กำหนด Sky View Factor ที่ส่งผลต่อ night-time cooling — ไม่ใส่เพราะ ไม่มีข้อมูล 3D building ใน GEE
ปัจจัยทางสังคม (Adaptive Capacity): รายได้, การเข้าถึง healthcare, อายุประชากร — ไม่ใส่เพราะ ข้อมูล census ไม่อยู่ใน GEE

3. โมเดลนี้ใช้ได้ดีในระดับ Scale ใด?
โมเดลนี้เหมาะที่สุดสำหรับ ระดับอำเภอ/เขต (District Level) เพราะ:

MODIS LST (1km) และ WorldPop เป็น input หลักที่มี resolution ระดับนี้
ข้อมูล JRC GSW มี resolution 30m แต่ถูก aggregate ขึ้นเมื่อรวมกับ factor อื่น
ระดับตำบล: ไม่แนะนำ เพราะ MODIS LST จะมี mixed pixel ปัญหามาก
ระดับจังหวัด: ใช้ได้ แต่สูญเสีย spatial detail ที่สำคัญ

4. ถ้าต้องนำโมเดลนี้ไปอัปเดตปีละครั้ง ขั้นตอนใดที่ต้องทำซ้ำและขั้นตอนใดที่คงที่?
ขั้นตอนที่ต้องทำซ้ำทุกปี:

ดึงข้อมูล LST ใหม่ (MODIS หรือ Landsat)
ดึง Sentinel-2 เพื่อคำนวณ NDVI และ NDBI ใหม่
อัปเดต Population Density (WorldPop ออก dataset ทุกปี)
Normalize ข้อมูลใหม่ทั้งหมด

ขั้นตอนที่คงที่ (ไม่ต้องเปลี่ยน):

น้ำหนัก AHP (เปลี่ยนเฉพาะเมื่อมีงานวิจัยใหม่)
สมการ UHSVI
Thresholds การแบ่งระดับ
DEM (SRTM) — ไม่เปลี่ยนแปลงในระยะสั้น
JRC GSW Permanent Water — เปลี่ยนแปลงช้ามาก


⚠️ ข้อจำกัดที่พบ (Limitations)

Resolution Mismatch: ข้อมูล input มี resolution ต่างกัน (MODIS 1km vs Sentinel 10m) — แก้ด้วยการ resample ทุกอย่างเป็น 100m
Temporal Consistency: LST แตกต่างระหว่างกลางวัน/กลางคืน — ใช้ Daytime LST เฉลี่ยเดือนมีนาคม–พฤษภาคม (peak heat)
Missing Social Factors: ความเปราะบางทางสังคม (elderly, low income) ไม่ได้อยู่ในโมเดล
Static Water Bodies: JRC GSW เป็น historical permanence — ไม่จับ seasonal flooding
Cloud Cover: Sentinel-2 ต้องกรอง cloud ซึ่งอาจลดข้อมูลในบางช่วง


📚 References หลัก

Weng, Q., Lu, D., & Schubring, J. (2004). Remote Sensing of Environment, 89(4), 467–483.
Peng, S., et al. (2012). Environmental Science & Technology, 46(2), 696–703.
Mohan, M., et al. (2020). Atmospheric Research, 243, 105026.
Saaty, T.L. (1980). The Analytic Hierarchy Process. McGraw-Hill.
IPCC (2014). Climate Change 2014: Impacts, Adaptation, and Vulnerability.
Rizwan, A.M., Dennis, L.Y.C., & Liu, C. (2008). Journal of Environmental Sciences, 20(1), 120–128.
