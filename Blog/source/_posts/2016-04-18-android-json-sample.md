---
layout: post
title: '[Android] Json sample 1/2 '
date: 2016-04-18 22:15
comments: true
categories: Android
tag:
	- json
---

[JSON](https://zh.wikipedia.org/wiki/JSON)（JavaScript Object Notation）是一種由道格拉斯·克羅克福特構想設計、輕量級的資料交換語言，以文字為基礎，且易於讓人閱讀。儘管JSON是Javascript的一個子集，但JSON是獨立於語言的文字格式，並且採用了類似於C語言家族的一些習慣。
以上採自Wikipedia. 

因為我還沒寫parser, 所以這篇只有介紹該怎麼都出一個你心中那SPEC的Json array
```json Sample.json
{
  "Inputs": {
    "input1": {
      "ColumnNames": [
        "symboling",
        "normalized-losses",
        "make",
        "fuel-type",
        "aspiration",
        "num-of-doors",
        "body-style",
        "drive-wheels",
        "engine-location",
        "wheel-base",
        "length",
        "width",
        "height",
        "curb-weight",
        "engine-type",
        "num-of-cylinders",
        "engine-size",
        "fuel-system",
        "bore",
        "stroke",
        "compression-ratio",
        "horsepower",
        "peak-rpm",
        "city-mpg",
        "highway-mpg",
        "price"
      ],
      "Values": [
        [
          "0",
          "0",
          "value",
          "value",
          "value",
          "value",
          "value",
          "value",
          "value",
          "0",
          "0",
          "0",
          "0",
          "0",
          "value",
          "value",
          "0",
          "value",
          "0",
          "0",
          "0",
          "0",
          "0",
          "0",
          "0",
          "0"
        ],
        [
          "0",
          "0",
          "value",
          "value",
          "value",
          "value",
          "value",
          "value",
          "value",
          "0",
          "0",
          "0",
          "0",
          "0",
          "value",
          "value",
          "0",
          "value",
          "0",
          "0",
          "0",
          "0",
          "0",
          "0",
          "0",
          "0"
        ]
      ]
    }
  },
  "GlobalParameters": {}
}
```
謹記
{}  這個叫做JSONObeject
[]  這個叫做JSONArray
所以上述的案例大概就是
```java Sample.activity
String[] columeName = {"symboling", "normalized-losses", "make", "fuel-type", "aspiration", "num-of-doors", "body-style", "drive-wheels", "engine-location", "wheel-base", "length", "width", "height", "curb-weight", "engine-type", "num-of-cylinders", "engine-size", "fuel-system", "bore", "stroke", "compression-ratio", "horsepower", "peak-rpm", "city-mpg", "highway-mpg", "price"};
            String[] mValues = {"2", "164", "audi", "gas", "std", "four", "sedan", "fwd", "front", "99.8", "176.6", "66.2", "54.3", "2337", "ohc", "four", "109", "mpfi", "3.19", "3.4", "10", "102", "5500", "24", "30", "13950"};
            String[] _Values = {"0", "0", "value", "value", "value", "value", "value", "value", "value", "0", "0", "0", "0", "0", "value", "value", "0", "value", "0", "0", "0", "0", "0", "0", "0", "0"};
            JSONArray colume = new JSONArray();
            JSONArray values_1 = new JSONArray();
            JSONArray values_2 = new JSONArray();
            for (int i = 0; i < columeName.length; i++) {
                colume.put(columeName[i]);
                values_1.put(mValues[i]);
                values_2.put(mValues[i]);
            }
            JSONArray valuesArray = new JSONArray();
            valuesArray.put(values_1);
            valuesArray.put(values_2);


            JSONObject arrInput1 = new JSONObject();
            arrInput1.put("ColumnNames", colume);
            arrInput1.put("Values", valuesArray);
            JSONObject input1 = new JSONObject();
            input1.put("input1", arrInput1);

            JSONObject mInput = new JSONObject();

            mInput.put("Inputs", input1);
            JSONObject mGlobalParameters = new JSONObject();
            mInput.put("GlobalParameters", mGlobalParameters);
```