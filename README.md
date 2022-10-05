# COLLECTION-PROCESSING-AND-VISUALIZATION-OF-TEST-DATA-SET.Lab-2

Отчет по лабораторной работе #2 выполнил(а):
- Фоменко Андрей Васильевич
- РИ000024
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Познакомиться с программными средствами для организции
передачи данных между инструментами google, Python и Unity

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python
- Google-Sheets – Unity. При выполнении задания используйте видео-материалы и
исходные данные, предоставленные преподавателя курса.

Ход работы:


- В облачном сервисе google console подключить API для работы с google
sheets и google drive.

<img width="1091" alt="Снимок экрана 2022-10-05 в 18 29 11" src="https://user-images.githubusercontent.com/83164641/194100157-480ae566-d2f5-4ca2-85a9-ce6c2204ce0c.png">


- Реализовать запись данных из скрипта на python в google-таблицу. Данные
описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с
учётом стоимости игрового объекта в каждый период.

Код, который я использовал:

```py
import gspread
import numpy as np

gc = gspread.service_account(filename='unitydatasciense-364409-28441679f719.json')
sh = gc.open("UnitySheets")
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i - 1] - price[i - 2]) / price[i - 2]) * 100
        tempInf = str(tempInf)
        # tempInf = tempInf.replace('.' , ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)

```

 Получил на выходе: 
 
<img width="330" alt="Снимок экрана 2022-10-05 в 12 50 54" src="https://user-images.githubusercontent.com/83164641/194107240-94e1cf41-8954-4c0b-9eda-f499ac7b822b.png">


- Создать новый проект на Unity, который будет получать данные из google-
таблицы, в которую были записаны данные в предыдущем пункте.

<img width="688" alt="Снимок экрана 2022-10-05 в 18 58 21" src="https://user-images.githubusercontent.com/83164641/194107399-47d9262f-5763-4f91-90d4-1f2eb4aba1b1.png">

- Написать функционал на Unity, в котором будет воспризводиться аудио-
файл в зависимости от значения данных из таблицы.

```c#

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;


public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;

    public AudioClip normalSpeak;

    public AudioClip badSpeak;

    public AudioClip epicSpeak;

    public AudioClip goldCommonSpeak;

    public AudioClip legendarySpeak;

    private AudioSource selectAudio;

    private Dictionary<string, float> dataSet = new Dictionary<string, float>();

    private bool statusStart = false;

    private int i = 1;


    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart ==false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1vC9Yo4lNClUtGCJv8jA0ukm2wa8ePeeq-hfcFvsWZ0A/values/List1?key=AIzaSyBWeQwI4ktspDSSiNcy8Llry8aSnx-4rr0");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson =JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
        
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}


```

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных
с помощью линейной регрессии из лабораторной работы № 1 - (Развернуто ответил Видеороликом после третьего задания)

Написал такой код:

```py
import numpy as np
import gspread
import matplotlib.pyplot as plt

gc = gspread.service_account(filename='unitydatasciense-364409-28441679f719.json')
sh = gc.open("UnitySheets")

# define data, and change list to array
x = [3,21,22,34,54,34,55,67,89,99]
x = np.array(x)
y = [2,22,24,65,79,82,55,130,150,199]
y = np.array(y)
j = 0
#Show the effect of a scatter plot
plt.scatter(x,y)


#The basic linear regression model is wx+ b, and since this is a two-dimensional space, the model is ax+ b

def model(a, b, x):
    return a*x + b

#Tahe most commonly used loss function of linear regression model is the loss function of mean variance difference
def loss_function(a, b, x, y):
    num = len(x)
    prediction=model(a,b,x)
    return (0.5/num) * (np.square(prediction-y)).sum()

#The optimization function mainly USES partial derivatives to update two parameters a and b
def optimize(a,b,x,y):
    num = len(x)
    prediction = model(a,b,x)
    #Update the values of A and B by finding the partial derivatives of the loss function on a and b
    da = (1.0/num) * ((prediction -y)*x).sum()
    db = (1.0/num) * ((prediction -y).sum())
    a = a - Lr*da
    b = b - Lr*db
    return a, b

#iterated function, return a and b
def iterate(a,b,x,y,times):
    for i in range(times):
        a,b = optimize(a,b,x,y)
    return a,b

#Initialize parameters and display
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.0006

#For the first iteration, the parameter values, losses, and visualization after the iteration are displayed
a,b = iterate(a,b,x,y,1)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
j += 1
sh.sheet1.update(('C' + str(j)), str(loss))
plt.scatter(x,y)
plt.plot(x,prediction)


a,b = iterate(a,b,x,y,2)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
j += 1
sh.sheet1.update(('C' + str(j)), str(loss))
plt.scatter(x,y)
plt.plot(x,prediction)

a,b = iterate(a,b,x,y,3)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
j += 1
sh.sheet1.update(('C' + str(j)), str(loss))
plt.scatter(x,y)
plt.plot(x,prediction)

a,b = iterate(a,b,x,y,4)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
j += 1
sh.sheet1.update(('C' + str(j)), str(loss))
plt.scatter(x,y)
plt.plot(x,prediction)

a,b = iterate(a,b,x,y,5)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
j += 1
sh.sheet1.update(('C' + str(j)), str(loss))
plt.scatter(x,y)
plt.plot(x,prediction)

a,b = iterate(a,b,x,y,1000)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
j += 1
sh.sheet1.update(('C' + str(j)), str(loss))
plt.scatter(x,y)
plt.plot(x,prediction)

j = 0


```

##Задание 3 Самостоятельно разработать сценарий воспроизведения звукового
сопровождения в Unity в зависимости от изменения считанных данных в задании 2 (Развернуто ответил Видеороликом после третьего задания)


- Звуковые исходники были взяты из игры **Hearthstone**

- На основании величины Loss происходит звуковое сопроводение выпавшей карты из пака - чем **ниже** величина **loss** тем **выше** ценность,выпавшей карты)

- Код, который я написал:
```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;


public class HsTest : MonoBehaviour
{
    public AudioClip legendarySpeak;

    public AudioClip goldRareSpeak;

    public AudioClip rareSpeak;

    public AudioClip epicSpeak;

    public AudioClip goldCommonSpeak;

    public AudioClip foilLegendarySpeak;

    private AudioSource selectAudio;

    private Dictionary<string, float> dataSet = new Dictionary<string, float>();

    private bool statusStart = false;

    private int i = 1;


    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        
        
            if (dataSet["Mon_" + i.ToString()] > 1000 & statusStart == false & i != dataSet.Count)
            {
                StartCoroutine(PlaySelectAudioGoldCommonSpeak());
                Debug.Log(dataSet["Mon_" + i.ToString()]);
            }

            if (dataSet["Mon_" + i.ToString()] >= 500 & dataSet["Mon_" + i.ToString()] < 1000 & statusStart == false & i != dataSet.Count)
            {
                StartCoroutine(PlaySelectAudioRareSpeak());
                Debug.Log(dataSet["Mon_" + i.ToString()]);
            }

            if (dataSet["Mon_" + i.ToString()] < 500 & dataSet["Mon_" + i.ToString()] > 400 & statusStart == false & i != dataSet.Count)
            {
                StartCoroutine(PlaySelectAudioGoldRareSpeak());
                Debug.Log(dataSet["Mon_" + i.ToString()]);
            }

            if (dataSet["Mon_" + i.ToString()] < 400 & dataSet["Mon_" + i.ToString()] > 250 & statusStart == false & i != dataSet.Count)
            {
                StartCoroutine(PlaySelectAudioEpicSpeak());
                Debug.Log(dataSet["Mon_" + i.ToString()]);
            }

            if (dataSet["Mon_" + i.ToString()] < 250 & dataSet["Mon_" + i.ToString()] > 190 & statusStart == false & i != dataSet.Count)
            {
                StartCoroutine(PlaySelectAudioLegendarySpeak());
                Debug.Log(dataSet["Mon_" + i.ToString()]);
            }

            if (dataSet["Mon_" + i.ToString()] < 190 & statusStart ==false & i != dataSet.Count)
            {
                StartCoroutine(PlaySelectAudioFoilLegendarySpeak());
                Debug.Log(dataSet["Mon_" + i.ToString()]);
            }

            if (i > 7)
            {
                return;
            }
        
        
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1vC9Yo4lNClUtGCJv8jA0ukm2wa8ePeeq-hfcFvsWZ0A/values/List1?key=AIzaSyBWeQwI4ktspDSSiNcy8Llry8aSnx-4rr0");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson =JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
        
    }

    IEnumerator PlaySelectAudioFoilLegendarySpeak()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = foilLegendarySpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(5);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioGoldCommonSpeak()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goldCommonSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioRareSpeak()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = rareSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }

     IEnumerator PlaySelectAudioGoldRareSpeak()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goldRareSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }

     IEnumerator PlaySelectAudioEpicSpeak()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = epicSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }

     IEnumerator PlaySelectAudioLegendarySpeak()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = legendarySpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```

## Видеоролик пришлось сжать, так как в отчет нельзя загрузить файл больше 10MB



https://user-images.githubusercontent.com/83164641/194110542-c37d5e5d-37db-4a8c-b9ab-25ab69c6c567.mov



## Выводы

В данной рвботе я научился записывать данные в сервис GoogleSheets с помощью Python, а также считывать данные GoogleSheets по средствам c# скрипта для Unity. 

Самостоятельно разработал сценарий воспроизведения звукового
сопровождения в Unity, пререработав предложенный код под формат - На основании величины Loss происходит звуковое сопроводение выпавшей карты из пака - чем **ниже** величина **loss** тем **выше** ценность,выпавшей карты)

- Звуковые исходники были взяты из игры **Hearthstone**

bb

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**Lampovui (Fomenko Andrey)
