---
layout: post
title: "[JAVA] WorkbookFactory 기능 엑셀 업로드 xls, xlsx 지원"
categories: [study, java]
tags: [java , excel]
---


자바에서 엑셀을 읽는 작업을 할 때 

.xls은 HSSF

.xlsx는 XSSF

HSSF / XSSF는 각각의 확장자에게만 적용이 되고 둘다 사용하려면 SXSSF를 사용해야 한다.

하지만 SXSSF는 의존성이 강해서 jar가 많이 필요하다기에 사용하지 않았다.

찾아보니 WorkbookFactory라고 poi에 있는 놈을 이용해서 자동으로 사용 가능하게 할 수 있다더라.

(WorkbookFactory는 poi-ooxml에 존재한다)

최초 항목들 선언도 앞에 HSSF, XSSF 없이 그냥

```java
Workbook workbook = null

Sheet sheet = null

Row row = null

Cell cell = null
```


이렇게만 지정해줘도 사용이 가능하다

받아온 엑셀을 inputstream이나 file에 넣은다음

`worbook = WorkbookFactory.create(엑셀파일)`  

이것만 해주면 엑셀 확장자에 맞는 workbook을 반환해준다. 

poi 3.9 기준으로 5가지 타입을 받을 수 있다.

|   인풋 타입   |   반환타입   |
| --- | --- |
|   POIFSFileSystem   |   HSSFWorkbook   |
|   NPOIFSFileSystem   |   HSSFWorkbook   |
|   OPCPackage   |   XSSFWorkbook   |
|   InputStream   |   HSSFWorkbook / XSSFWorkbook   |
|   File   |   HSSFWorkbook / XSSFWorkbook   |

이후에는 다른 방법들과 똑같이

`sheet = workbook.getSheetAt(인덱스)`로 sheet를 읽어오고

`cell.getStringValue`해서 데이터를 읽어오면 된다.


