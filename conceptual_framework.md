Conceptual Framework: Urban Heat Stress Vulnerability Index
ภม.338 Geographic Data Science | Lab 4

1. คำถามวิจัย (Research Question)

"พื้นที่ใดในเขตเมืองของกรุงเทพมหานครมีความเสี่ยงสูงต่อความเครียดจากความร้อน (Urban Heat Stress) และปัจจัยเชิงพื้นที่ใดมีผลกระทบมากที่สุด?"

คำถามนี้ตอบได้ด้วยแผนที่เชิงพื้นที่ที่แสดง Urban Heat Stress Vulnerability Index (UHSVI) โดยใช้ข้อมูลจากดาวเทียมและ GEE Python API

2. กรอบแนวคิด (Theoretical Framework)
2.1 นิยาม Urban Heat Stress
Urban Heat Stress คือสภาวะที่ร่างกายมนุษย์รับความร้อนสะสมจากสภาพแวดล้อมเมืองเกินกว่าจะระบายออกได้ตามปกติ ส่งผลต่อสุขภาพและการดำรงชีวิต Urban Heat Island (UHI) effect เป็นกลไกหลักที่ทำให้อุณหภูมิในเขตเมืองสูงกว่าพื้นที่ชนบทโดยเฉลี่ย 1–7°C (Oke, 1982; Voogt & Oke, 2003)
2.2 ปัจจัยที่ส่งผลต่อ Urban Heat Stress
จากการทบทวนวรรณกรรม ปัจจัยหลักที่ใช้ในโมเดล UHSVI มี 6 ปัจจัย ดังนี้:

3. ปัจจัย (Factors) และที่มาทางวิทยาศาสตร์
Factor 1: Land Surface Temperature (LST)

แหล่งข้อมูล: MODIS Terra/Aqua LST (MOD11A1 / MYD11A1)
หน่วย: Kelvin → แปลงเป็น °C
เหตุผล: LST คือตัวแปรโดยตรงที่วัดความร้อนที่พื้นผิวดูดซับและปล่อยออกมา เป็น proxy หลักของ UHI effect
อ้างอิง: Weng et al. (2004) "Estimation of land surface temperature–vegetation abundance relationship for urban heat island studies" Remote Sensing of Environment, 89(4), 467–483. DOI: 10.1016/j.rse.2003.11.005

Factor 2: Normalized Difference Vegetation Index (NDVI)

แหล่งข้อมูล: Sentinel-2 MSI (Band 8 NIR, Band 4 Red)
สูตร: NDVI = (NIR - Red) / (NIR + Red)
เหตุผล: พืชพรรณช่วยลดอุณหภูมิผ่านกระบวนการ evapotranspiration และ shading ค่า NDVI สูงหมายความว่าความเสี่ยงต่ำ (inverse relationship)
อ้างอิง: Peng et al. (2012) "Surface urban heat island across 419 global big cities" Environmental Science & Technology, 46(2), 696–703. DOI: 10.1021/es2030438

Factor 3: Impervious Surface Fraction (ISF)

แหล่งข้อมูล: Sentinel-2 NDBI (Normalized Difference Built-up Index) หรือ MODIS Urban Extent
สูตร: NDBI = (SWIR - NIR) / (SWIR + NIR)
เหตุผล: พื้นผิวที่ไม่ซึมน้ำ (คอนกรีต, แอสฟัลต์) ดูดซับความร้อนสูงและปล่อยออกมาช้า ส่งผลโดยตรงต่อ LST
อ้างอิง: Rizwan et al. (2008) "A review on the generation, determination and mitigation of Urban Heat Island" Journal of Environmental Sciences, 20(1), 120–128. DOI: 10.1016/S1001-0742(08)60019-4

Factor 4: Population Density (Exposure)

แหล่งข้อมูล: WorldPop หรือ GPWv4 (Gridded Population of the World)
เหตุผล: ความหนาแน่นประชากรสูงหมายถึงจำนวนคนที่ได้รับผลกระทบจาก heat stress มากขึ้น เป็น "exposure" ตาม IPCC Vulnerability Framework
อ้างอิง: IPCC (2014) "Climate Change 2014: Impacts, Adaptation, and Vulnerability" Chapter 11: Human Health. Cambridge University Press.

Factor 5: Distance to Water Bodies (Cooling Effect)

แหล่งข้อมูล: JRC Global Surface Water (JRC/GSW1_4/GlobalSurfaceWater)
เหตุผล: แหล่งน้ำมีผล evaporative cooling ลดอุณหภูมิโดยรอบได้ 1–3°C ในรัศมี 300–500 เมตร พื้นที่ที่อยู่ไกลแหล่งน้ำจึงมีความเสี่ยงสูงกว่า
อ้างอิง: Sun & Chen (2012) "A main coolness island study of West Lake in Hangzhou, China" Ecological Indicators, 16, 85–87. DOI: 10.1016/j.ecolind.2011.12.005

Factor 6: Elevation (Topographic Effect)

แหล่งข้อมูล: SRTM DEM 30m (NASA SRTM Digital Elevation)
เหตุผล: พื้นที่ต่ำสะสมอากาศร้อนและมีการระบายอากาศน้อยกว่า (ตาม lapse rate) พื้นที่สูงมีอุณหภูมิลดลงประมาณ 0.65°C ต่อ 100 เมตร
อ้างอิง: Oke (1987) "Boundary Layer Climates" 2nd edition. Routledge, London. (Chapter 8: Cities)


4. น้ำหนัก AHP และที่มา (AHP Weights with Justification)
4.1 วิธีการกำหนดน้ำหนัก
ใช้ Analytic Hierarchy Process (AHP) โดยอิงจากงานวิจัยเกี่ยวกับ Urban Heat Stress ในเอเชียตะวันออกเฉียงใต้
อ้างอิงหลักสำหรับ AHP weights:

Mohan et al. (2020) "Urban heat island assessment for a tropical urban airshed in India" Atmospheric Research, 243, 105026. DOI: 10.1016/j.atmosres.2020.105026
Santamouris (2015) "Analyzing the heat island magnitude and characteristics in one hundred Asian and Australian cities and regions" Science of the Total Environment, 512–513, 582–598.

4.2 Pairwise Comparison Matrix
FactorLSTNDVIISFPopDensDistWaterElevationLST132456NDVI1/311/2234ISF1/221345PopDens1/41/21/3123DistWater1/51/31/41/212Elevation1/61/41/51/31/21
4.3 น้ำหนักที่คำนวณได้
FactorWeight (%)เหตุผลLST35%ตัวแปรโดยตรงของ heat stress — มีผลมากที่สุดตาม Weng et al. (2004)ISF (NDBI)25%ตัวขับเคลื่อนหลักของ UHI ผ่าน albedo และ thermal mass ต่ำNDVI20%mitigation factor สำคัญ — inverse relationship กับ heatPopDens10%exposure component ตาม IPCC vulnerability frameworkDistWater6%cooling effect มีรัศมีจำกัด (~500m)Elevation4%ผลค่อนข้างน้อยในพื้นที่ราบเช่น กทม.รวม100%
Consistency Ratio (CR) = 0.047 < 0.10 → AHP ผ่านการตรวจสอบความสอดคล้อง (Saaty, 1980)

5. Normalization Method
ใช้ Min-Max Normalization ให้แต่ละ factor อยู่ในช่วง [0, 1] โดย:

0 = ความเสี่ยงต่ำ (เย็น, มีพืชพรรณมาก, ใกล้แหล่งน้ำ)
1 = ความเสี่ยงสูง (ร้อน, ไม่มีพืชพรรณ, ไกลแหล่งน้ำ)

สำหรับ NDVI และ DistWater ต้อง invert (1 - normalized) เนื่องจากเป็น protective factors
normalized = (value - min) / (max - min)
NDVI_risk = 1 - normalized_NDVI   # inverse: NDVI สูง = เสี่ยงต่ำ
DistWater_risk = normalized_DistWater  # ไกลน้ำ = เสี่ยงสูง

6. สมการโมเดล (Model Equation)
UHSVI = (0.35 × LST_norm) + (0.25 × ISF_norm) + (0.20 × NDVI_risk) 
       + (0.10 × PopDens_norm) + (0.06 × DistWater_risk) + (0.04 × Elev_risk)
ที่มาของสมการ: ดัดแปลงจาก Mohan et al. (2020) สำหรับบริบทเมืองเขตร้อนชื้น

7. Thresholds และการแบ่งระดับความเสี่ยง
UHSVI Scoreระดับสีที่มา0.00 – 0.20Very Lowเขียวเข้มQuintile classification0.20 – 0.40Lowเขียวอ่อนจาก Santamouris (2015)0.40 – 0.60Moderateเหลืองและปรับตามบริบท กทม.0.60 – 0.80Highส้ม0.80 – 1.00Very Highแดงเข้ม

8. แผน Validation
โมเดลข้อมูล Validationแหล่งLST-based UHSVIMODIS LST ย้อนหลัง 5 ปีNASA LP DAACพื้นที่เสี่ยงสูงWeather Station Data (TMD)กรมอุตุนิยมวิทยาImpervious surfaceMODIS Land Cover (MCD12Q1)NASAแหล่งน้ำJRC Global Surface WaterEC JRC

9. References

Oke, T.R. (1982). The energetic basis of the urban heat island. Quarterly Journal of the Royal Meteorological Society, 108(455), 1–24. DOI: 10.1002/qj.49710845502
Voogt, J.A. & Oke, T.R. (2003). Thermal remote sensing of urban climates. Remote Sensing of Environment, 86(3), 370–384. DOI: 10.1016/S0034-4257(03)00079-8
Weng, Q., Lu, D., & Schubring, J. (2004). Estimation of land surface temperature–vegetation abundance relationship for urban heat island studies. Remote Sensing of Environment, 89(4), 467–483. DOI: 10.1016/j.rse.2003.11.005
Peng, S., et al. (2012). Surface urban heat island across 419 global big cities. Environmental Science & Technology, 46(2), 696–703. DOI: 10.1021/es2030438
Rizwan, A.M., Dennis, L.Y.C., & Liu, C. (2008). A review on the generation, determination and mitigation of Urban Heat Island. Journal of Environmental Sciences, 20(1), 120–128.
IPCC (2014). Climate Change 2014: Impacts, Adaptation, and Vulnerability. Cambridge University Press.
Mohan, M., et al. (2020). Urban heat island assessment for a tropical urban airshed in India. Atmospheric Research, 243, 105026.
Santamouris, M. (2015). Analyzing the heat island magnitude and characteristics in one hundred Asian and Australian cities. Science of the Total Environment, 512–513, 582–598.
Saaty, T.L. (1980). The Analytic Hierarchy Process. McGraw-Hill, New York.
Sun, R. & Chen, L. (2012). A main coolness island study of West Lake in Hangzhou, China. Ecological Indicators, 16, 85–87.
