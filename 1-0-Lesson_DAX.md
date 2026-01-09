<img src="picture\images-0.png" alt="alt text" width="100">

<br>

# 📍 DAX

<br>

# Sumation formular

## 1. SUM
``` md
SUM(<ColumnName>)
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em" >

``` md
Tourist_Amt = SUM('นักท่องเที่ยวต่างชาติ'[จำนวนคน])
```
<img src="picture\DAX\image.png" alt="alt text" width="500">

</div>
</details>


## 2. SUMX
``` md
SUMX(<table>, <expression>)
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_AmtX = SUMX('นักท่องเที่ยวต่างชาติ','นักท่องเที่ยวต่างชาติ'[จำนวนคน]*0.8)
```
<img src="picture\DAX\image-1.png" alt="alt text" width="500">

</div>
</details>

<br>

# Min/Max formular

## 3. MIN
``` md
MIN(<column>)
MIN(<expression1>, <expression2>)
MIN(<ColumnsName_Or_Scalar1>, [, Scalar2])
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_MIN = MIN('นักท่องเที่ยวต่างชาติ'[จำนวนคน])
```
<img src="picture\DAX\image-2.png" alt="alt text" width="500">
</div>
</details>


## 4. MAX
``` md
MAX(<column>)
MAX(<expression1>, <expression2>)
MAX(<ColumnsName_Or_Scalar1>, [, Scalar2])
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_MAX = MAX('นักท่องเที่ยวต่างชาติ'[จำนวนคน])
```
<img src="picture\DAX\image-3.png" alt="alt text" width="500">
</div>
</details>

<br>

# CAL / FILTER

## 5. CALCULATE

``` md
CALCULATE(<expression>[, <filter> [, <filter2> [, ...]]])
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_CALCULATE = CALCULATE([Tourist_Amt],'นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2)

Tourist_CALCULATE = CALCULATE(SUM('นักท่องเที่ยวต่างชาติ'[จำนวนคน]),'นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2)
```
<img src="picture\DAX\image-4.png" alt="alt text" width="500">

**ข้อสังเกตุ**

- จะเห็นว่า [รหัสทวีป] ใช้ `CALCULATE()` ที่ "บังคับกรอง" ว่าให้ดูเฉพาะ [รหัสทวีป] = 2 เสมอ
    - มัน ไม่สนว่า Row Context ของ Visual จะเป็น รหัสทวีป 1,3,4,5,6,7

สรุปความเข้าใจ


|รหัสทวีป|	Tourist_Amt|	Tourist_CALCULATE|
|-----|-----|-----|
|1| 180,261,744|	59,455,066 (ถูก override)|
|**2**| **59,455,066**|	**59,455,066**|
|3|	12,561,911|	59,455,066 (ถูก override)|
|4|	15,112,016|	59,455,066 (ถูก override)|
|5|	9,313,997|	59,455,066 (ถูก override)|
|6|	6,641,565|	59,455,066 (ถูก override)|
|7|	1,670,078|	59,455,066 (ถูก override)|

</div>
</details>


## 6. KEEPFILTERS
ต่างจาก filter ปกติ ตรงที่จะแสดงเพียงแค่ค่าที่ Filter ค่าอื่นเป็น 0

``` md
CALCULATE(<expression>,[KEEPFILTERS(<expression>)])
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_ Keepfilter = CALCULATE([Tourist_Amt],KEEPFILTERS('นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2))
หรือ
Tourist_ Keepfilter = CALCULATE(SUM('นักท่องเที่ยวต่างชาติ'[จำนวนคน]),KEEPFILTERS('นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2))
```
<img src="picture\DAX\image-5.png" alt="alt text" width="500">

</div>
</details>


## 7. FILTER

ใช้กับ SUMX
```md 
SUMX(<table>, <expression>)
```

``` md
FILTER(<table>,<filter>)
FILTER(<table>,<filterExpression>)
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_Filter = SUMX(FILTER('นักท่องเที่ยวต่างชาติ','นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2),'นักท่องเที่ยวต่างชาติ'[จำนวนคน]*0.8)

# หรือ 
CALCULATE([Tourist_Amt],KEEPFILTERS('นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2))*0.8
```
<img src="picture\DAX\image-6.png" alt="alt text" width="500">

</div>
</details>


## 8. ALLEXCEPT
**กรองแค่เฉพาะ slicer ที่เลือกเท่านั้น** นอกนั้น ignore

``` md
ALLEXCEPT(Table, FilterExpression)
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
RunningTotal_PartitionByYear = 
CALCULATE(
    SUM(RawTable[Amount]),
    ALLEXCEPT(RawTable, RawTable[Year]),  -- ✅ ลบตัวกรองทุกอย่าง ยกเว้น Year (คำนวณเฉพาะปีที่เลือก)
    RawTable[Date] <= MAX(RawTable[Date]) 
)

```
<img src="picture\DAX\image-7.png" alt="alt text" width="500">
<img src="picture\DAX\image-8.png" alt="alt text" width="500">
</div>

<div style="margin-left:1em">

``` md
// หรือใช้ แบบนี้ได้

RunningTotal_PartitionByYear = 
CALCULATE(
    SUM(RawTable[Amount]),
    REMOVEFILTERS(RawTable[Month]),
    RawTable[Date] <= MAX(RawTable[Date]) 
)
```
</div>
</details>

<br>

# Logic
## 9. AND
``` md
AND(<Logical1>,<Logical2>)
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_AND = CALCULATE([Tourist_Amt],FILTER('นักท่องเที่ยวต่างชาติ','นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2 && 'นักท่องเที่ยวต่างชาติ'[รหัสประเทศ]=17))
หรือ
Tourist_AND2 = CALCULATE([Tourist_Amt],KEEPFILTERS('นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2 && 'นักท่องเที่ยวต่างชาติ'[รหัสประเทศ]=17))
```
<img src="picture\DAX\image-9.png" alt="alt text" width="500">

</div>
</details>


## 10. OR
``` md
OR(<Logical1>,<Logical2>)
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_OR = CALCULATE([Tourist_Amt],FILTER('นักท่องเที่ยวต่างชาติ','นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2 || 'นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=6))
หรือ
Tourist_AND2 = CALCULATE([Tourist_Amt],KEEPFILTERS('นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2 || 'นักท่องเที่ยวต่างชาติ'[รหัสประเทศ]=17))
```
<img src="picture\DAX\image-10.png" alt="alt text" width="500">

</div>
</details>


## 11. IN
``` md
<column> IN {..,...,...}
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_IN = CALCULATE([Tourist_Amt],FILTER('นักท่องเที่ยวต่างชาติ','นักท่องเที่ยวต่างชาติ'[รหัสทวีป] IN {2,4,5}))
หรือ
Tourist_IN = CALCULATE([Tourist_Amt],FILTER('นักท่องเที่ยวต่างชาติ','นักท่องเที่ยวต่างชาติ'[รหัสทวีป] = 2 || 
                                             'นักท่องเที่ยวต่างชาติ'[รหัสทวีป] =  4 || 
                                             'นักท่องเที่ยวต่างชาติ'[รหัสทวีป] = 5}))
```
<img src="picture\DAX\image-11.png" alt="alt text" width="500">

</div>
</details>


## 12. VAR
``` md
VAR <NAME> = <Expression>
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_VAR = 
VAR Europe = CALCULATE([Tourist_Amt],'นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=2) 
VAR Africa = CALCULATE([Tourist_Amt],'นักท่องเที่ยวต่างชาติ'[รหัสทวีป]=7)

Return
Europe-Africa
```
<img src="picture\DAX\image-12.png" alt="alt text" width="500">

</div>
</details>


<br>
<br>

# CREATE TABLE Formular

## 13. SELECTCOLUMNS

**สรุป**  
``` md
- ADDCOLUMNS    : เพิ่มคอลัมน์ใหม่ให้กับตารางเดิม (และรักษาคอลัมน์ที่มีอยู่)
- SELECTCOLUMNS : สร้างตารางใหม่โดยเลือกเฉพาะคอลัมน์ที่ต้องการและเปลี่ยนชื่อได้
```

``` md
NewTable=
   SELECTCOLUMNS(
   <Table ต้นทาง ที่จะเอามา>,
   <Columns_name>,      การกระทำ [Columns_nameของต้นทาง],
   <Columns_name_2>,    การกระทำ [Columns_nameของต้นทาง]_2,
    )
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
นักท่องเที่ยวต่างชาติ2 = 
SELECTCOLUMNS(
    'นักท่องเที่ยวต่างชาติ',
    "รหัสทวีป2", [รหัสทวีป],
    "รหัสประเทศ2", [รหัสประเทศ],
    "จำนวนคน2", [จำนวนคน]*0.5
)
```
<img src="picture\DAX\image-13.png" alt="alt text" width="300">

ได้จำนวนคน หารด้วย 2

</div>
</details>


## 14. SUMMARIZE
``` md
SUMMARIZE(table, 
  <GroupByColumn1>,
  <GroupByColumn2>,
  ...,
  "NewColumnName", expression
)
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_Summarize = 
    AVERAGEX(
        SUMMARIZE(
            'นักท่องเที่ยวต่างชาติ',
            'นักท่องเที่ยวต่างชาติ'[ปี],
            "Total_Person", SUM('นักท่องเที่ยวต่างชาติ'[จำนวนคน])
        ),
    [Total_Person]
    )
```
<img src="picture\DAX\image-14.png" alt="alt text" width="500">
</div>

<div>
สังเกตว่า

- คอลัมน์ จำนวนคน กับ Tourist_Summarize ดูแต่ละปีจะเท่ากัน เพราะมันคือ Pivot sum
- แต่ Total จำนวนคนคือ บวกกัน , Tourist_Summarize คือ Average
- หากใช้ Average จะคำนวณค่าเฉลี่ย ของแต่ละ ปี ซึ่ง เราอยากได้ว่า แต่ละปี เอามาบวกกันก่อน แล้วจึงหาค่าเฉลี่ย

<img src="picture\DAX\image-15.png" alt="alt text" width="500">

จะเห็นว่า เรา group ตาม ปี
- รหัสทวีป 1 ⇒ group ตาม ปี (หาผลรวมแต่ละปี) แล้วหาค่าเฉลี่ย

</div>
</details>


## 15. SELECTEDVALUE
``` md
009_Criteria_MTD_vs_PY = 
VAR TM = SELECTEDVALUE('PP_WorkdoneReport VW_LogisticReport'[Topic Main]) 
VAR T = SELECTEDVALUE('PP_WorkdoneReport VW_LogisticReport'[Topic])

RETURN
IF([004_MTD_vs_PY] = " "," ",
    IF(AND(AND([004_MTD_vs_PY]<0, TM = "Freight"), T IN {"Expense (MB)"}), -1,
        IF(AND(AND([004_MTD_vs_PY]>0, TM = "Freight"), T = "Expense (MB)"), 0)
    )
)
```

<br>
<br>

# Other / Special

## 16. SWITCH
``` md
SWITCH(<Expression>, <value>, <result>[, <value>, <result>] ... [, <Else>])
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">
Multiple IF

``` md
Tourist_SWITCH = SWITCH(TRUE(),
                        [Tourist_Amt]<=1000000, "Low",
                        [Tourist_Amt]>1000000 && [Tourist_Amt]<=5000000, "Medium",
                        [Tourist_Amt]>5000000, "High")
```
<img src="picture\DAX\image-16.png" alt="alt text" width="500">

</div>
</details>


## 17. RELATED
``` md
RELATED(<columnname>)
```
<details style="background-color:#E0F4E1; cursor:pointer; padding: 0.55em 1em; border-radius: 6px;">
<summary> Example </summary>
<div style="margin-left:1em">

``` md
Tourist_RELATED = CALCULATE([Tourist_Amt],FILTER('นักท่องเที่ยวต่างชาติ',RELATED('ทวีป'[ชื่อทวีป])="โอเชียเนีย"))
```

สังเกตว่า Table : นักท่องเที่ยวต่างชาติ กับ Table : ทวีป อยู่คนละ Table กัน หากไม่ใส่ Related จะทำให้ค่า Error

**จะใช้ Table : นักท่องเที่ยวต่างชาติ เราก็รู้แต่รหัสทวีป แต่ไม่รู้ชื่อทวีป**

<img src="picture\DAX\image-17.png" alt="alt text" width="500">

</div>
</details>


## 18. UNICHAR

<details>
<summary> DETAIL </summary>
<div style="margin-left:1em">

1. **Box Drawings Down And Right**
    - **รหัส Unicode**: 9495
    - **UNICHAR**: `UNICHAR(9495)`
    - **ลักษณะ**: ┌
    - **การใช้งาน**: ใช้เพื่อแสดงมุมล่างขวาของกล่อง หรือกรอบที่เปิดออกไปทางขวาและลงด้านล่าง
2. **Box Drawings Down And Left**
    - **รหัส Unicode**: 9497
    - **UNICHAR**: `UNICHAR(9497)`
    - **ลักษณะ**: └
    - **การใช้งาน**: ใช้เพื่อแสดงมุมล่างซ้ายของกล่อง หรือกรอบที่เปิดออกไปทางซ้ายและลงด้านล่าง
3. **Box Drawings Up And Right**
    - **รหัส Unicode**: 9496
    - **UNICHAR**: `UNICHAR(9496)`
    - **ลักษณะ**: ┐
    - **การใช้งาน**: ใช้เพื่อแสดงมุมบนซ้ายของกล่อง หรือกรอบที่เปิดออกไปทางขวาและขึ้นด้านบน
4. **Box Drawings Up And Left**
    - **รหัส Unicode**: 9498
    - **UNICHAR**: `UNICHAR(9498)`
    - **ลักษณะ**: ┘
    - **การใช้งาน**: ใช้เพื่อแสดงมุมบนขวาของกล่อง หรือกรอบที่เปิดออกไปทางซ้ายและขึ้นด้านบน

</div>
</details>


## 19. FORMAT
<details>
<summary> DETAIL </summary>
<div style="margin-left:1em">

เปลี่ยนค่า เลข → ตัวหนังสือ  

``` md
FORMAT(([003_MTD_Act_ORG] - [002_MTD_BG_ORG])/10^6, "#,##0.00")
```

</div>
</details>


## 20. ISINSCOPE
- ตัวอย่างการใช้:
    
    สมมุติว่าคุณมีตารางข้อมูลที่มีคอลัมน์ `Product` และ `Sales` และต้องการทำการคำนวณยอดขายรวมที่แตกต่างกันตามระดับที่เลือกในรายงาน เช่น เมื่อเลือก `Product` ในรายงาน คุณอาจต้องการแสดงผลการคำนวณยอดขายต่อแต่ละสินค้าหรือยอดรวมทั้งหมด
    
``` md
Sales Amount =
IF(
    ISINSCOPE(Products[Product]),
    SUM(Sales[Amount]),
    CALCULATE(SUM(Sales[Amount]), ALL(Products))
)
```
<details>
<summary> DETAIL </summary>
<div style="margin-left:1em">

ในตัวอย่างนี้:

- `ISINSCOPE(Products[Product])`: เช็คว่าคอลัมน์ `Product` อยู่ใน scope หรือไม่
- ถ้าอยู่ใน scope (`TRUE`): แสดงยอดขายแยกตามสินค้า
- ถ้าไม่อยู่ใน scope (`FALSE`): คำนวณยอดขายรวมทั้งหมด

</div>
</details>


