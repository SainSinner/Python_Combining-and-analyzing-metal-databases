# Python_Combining and analyzing metal databases
 Consolidation of databases and analysis of the effect of melt composition on the mechanical properties of rolled products.

На предприятии существует проблема связанная с длительностью анализа влияния факторов производства на свойства конечного изделия.

Сейчас информация о испытаниях разных переделов ведется в разных таблицах Excel, что увеличивает время анализа.

Озадачившись вопросом: каким образом химический состав плавки влияет на механические свойства? 

Первым делом приступил к структурированию первой формы таблицы в которой указаны номера плавок и их химические составы.

Изначально данные о плавках были разделены по месяцам на листы файла и представлены в следующем виде.

![image](https://user-images.githubusercontent.com/35117005/192804766-1bb3fdb9-3c74-4a48-84e9-b79381229fea.png)

Для адекватного отображения названия столбцов применил следующее переименование для всех рассматриваемых месяцв.

import pandas as pd
import numpy as np
df = pd.read_excel('Data_melt.xlsx', sheet_name=None)

df['01-31.01'].rename(columns = {'Химический состав' : df['01-31.01'].iat[0,6], 
                                 'Unnamed: 7' : df['01-31.01'].iat[0,7], 
                                 'Unnamed: 8' : df['01-31.01'].iat[0,8], 
                                 'Unnamed: 9' : df['01-31.01'].iat[0,9],
                                 'Unnamed: 10' : df['01-31.01'].iat[0,10],
                                 'Unnamed: 11' : df['01-31.01'].iat[0,11],
                                 'Unnamed: 12' : df['01-31.01'].iat[0,12],
                                 'Unnamed: 13' : df['01-31.01'].iat[0,13],
                                 'Unnamed: 14' : df['01-31.01'].iat[0,14],
                                 'Unnamed: 15' : df['01-31.01'].iat[0,15],
                                 'Unnamed: 16' : df['01-31.01'].iat[0,16],
                                 'Unnamed: 17' : df['01-31.01'].iat[0,17],
                                 'Unnamed: 18' : df['01-31.01'].iat[0,18],
                                 'Unnamed: 19' : df['01-31.01'].iat[0,19],
                                 'Unnamed: 20' : df['01-31.01'].iat[0,20],
                                 'Unnamed: 21' : df['01-31.01'].iat[0,21],
                                 'Unnamed: 22' : df['01-31.01'].iat[0,22],
                                 'Unnamed: 23' : df['01-31.01'].iat[0,23],
                                 'Unnamed: 24' : df['01-31.01'].iat[0,24],
                                 'Unnamed: 25' : df['01-31.01'].iat[0,25],
                                 'Unnamed: 26' : df['01-31.01'].iat[0,26],
                                 'Unnamed: 27' : df['01-31.01'].iat[0,27],
                                 'Unnamed: 28' : df['01-31.01'].iat[0,28],
                                 'Unnamed: 29' : df['01-31.01'].iat[0,29]
                                }, inplace = True)

Далее удалил первую строку, потому что она болше не нужна.

df['01-31.01'] = df['01-31.01'].drop(index=[0])

Провдоим реиндексацию.

df['01-31.01'] = df['01-31.01'].reset_index(drop=True)

Полученный полученную таблицу выделяем в отдельный DataFrame.

df['01-31.01']['Date'] = '01.2022'

 Проделываем аналогичную последовательность действий, изложенную выше, для всех листов файла. После чего объединяем все месяца в один DataFrame.
 
df_melt = pd.concat ([df['01-31.01'], df['01-28.02'], df['01-31.03'], df['01-30.04'], df['01-31.05'], df['01-30.06']],ignore_index=True)

Выводим информацию по получившемуся DF.

df_melt.info(verbose=True)
df_melt.columns

Дополним строки, в которых пропущенно значение в столбце с датой, значением из вышерасположенной строки которая имеет значение.

df_melt["Дата отгрузки"] = df_melt["Дата отгрузки"].fillna(method="bfill")

Удаляем последнюю строку DF по причине отсутсвия в ней данных.

df_melt.drop(df_melt.index[-1], axis=0, inplace=True)

Так как для дальнейших манипуляций необходимо изменить размерность данных, указываем интрересующим нам столбцам верные форматы данных.

df_melt = df_melt.astype({'Дата отгрузки' : 'int64', '№ плавки' : 'int64'})
df_melt = df_melt.astype({'O' : 'float64', 'Pb' : 'float64', 'Nb' : 'float64', 'C' : 'float64', 'Si' : 'float64', 'Mn' : 'float64', 'P' : 'float64', 'S' : 'float64',
       'Ni' : 'float64', 'Cr' : 'float64', 'Cu' : 'float64', 'Al' : 'float64', 'As' : 'float64', 'N' : 'float64', 'Mo' : 'float64', 'V' : 'float64', 'B' : 'float64', 'Sn' : 'float64', 'Ceq' : 'float64', 'Sn+Mo' : 'float64',
       'Ti' : 'float64', 'W' : 'float64', 'Ca' : 'float64', 'Sb' : 'float64', 'Вес' : 'float64',})
       
Снова сбрасываем индекс и проверяем смену формата данных столбцов.

df_melt.reset_index(drop=True)
df_melt.info(verbose=True)

Проводим проверку наличия нулевых и не нулевых значений.

df_melt["Дата отгрузки"].isna()

Заменяем все Nan на 0.

df_melt = df_melt.fillna(value = 0)

Теперь заменяем все 0 на Nan, чтобы они не учитывались при дальнейших расчётах.

df_melt = df_melt.replace(0, np.nan)

Удаляем столбцы которые нас не интересуют.

df_melt.drop(['Дата отгрузки', '№ вагона/Сертификат качества', 'Сечение',
              'Тех карта', 'Вес', 'Примечание', 'Date',], axis = 1, inplace = True)

Сортируем таблицу по номеру плавки.

df_melt.sort_values(by=['№ плавки'])

Удаляем дубликаты строк окторые образовались в связи с удалением объема выплавок.

df_melt.drop_duplicates(inplace = True)

Присваиваем новый индекс через столбец "№ плавки".

df_melt.set_index('№ плавки',drop = False)

Обабатываем DF по катанке.

df = pd.read_excel('Data_wire_rod.xlsx', sheet_name=None)
df['катанка'].rename(columns = {'Unnamed: 0' : 'ИМЯ','Unnamed: 5' : 'ПЛАВКА','Unnamed: 6' : 'СТАЛЬ','Unnamed: 7' : 'НД','Unnamed: 8' : 'Dном, мм','Unnamed: 9' : 'Dфак, мм','Unnamed: 10' : 'σв, Н/мм2','Unnamed: 11' : 'σт, Н/мм2','Unnamed: 12' : 'σв/σт','Unnamed: 13' : 'δmax, %','Unnamed: 14' : 'Ψ,%','Unnamed: 15' : 'δ5, %','Unnamed: 16' : 'δ10, %','Unnamed: 17' : 'окалина, %','Unnamed: 18' : 'Изгиб','Unnamed: 19' : 'осадка','Unnamed: 20' : 'перегиб','Unnamed: 21' : 'ПРИМ','Unnamed: 22' : 'ДАТА','Unnamed: 23' : 'ЦЕЛЬ','Unnamed: 24' : 'оператор','Unnamed: 25' : 'протокол','Unnamed: 26' : '№заказа','Unnamed: 27' : 'σв, МПа','Unnamed: 28' : 'Ψ, %','Unnamed: 29' : 'окалина','Unnamed: 30' : 'овал','Unnamed: 31' : 'Бригада №','Unnamed: 32' : 'Приложение по выплавке',}, inplace = True)
df['катанка'] = df['катанка'].drop(index=[0, 1, 2])
df['катанка'] = df['катанка'].reset_index(drop=True)
df_wire_rod = df['катанка']

Обрабатываем DF по неоцинкованной проволоке.

df_wire = pd.read_excel('Data_wire.xlsx', sheet_name=None)
df_wire['неоц'].rename(columns = {'ИМЯ' : 'стан', 'Unnamed: 2' : 'бухта', 'Unnamed: 3': '№', 'повторы' : 'σв', 'Unnamed: 21' : 'δ' }, inplace = True)
df_wire['неоц'].drop(['Unnamed: 23', 'Unnamed: 24', 'Unnamed: 25', 'Unnamed: 26', 'Unnamed: 27', 'Unnamed: 28', 'Unnamed: 29'], axis = 1, inplace = True)
df_wire['неоц'] = df_wire['неоц'].drop(index=[0])
df_wire['неоц'] = df_wire['неоц'].reset_index(drop=True)
df_wire = df_wire['неоц']

Меняем местами столбцы и присваиваем новые названия столбцам DF по катанке.

df_wire_rod = df_wire_rod [['ПЛАВКА', 'ИМЯ', 'Unnamed: 1', 'Unnamed: 2', 'Unnamed: 3', 'Unnamed: 4', 'СТАЛЬ', 'НД', 'Dном, мм', 'Dфак, мм', 'σв, Н/мм2', 'σт, Н/мм2',
       'σв/σт', 'δmax, %', 'Ψ,%', 'δ5, %', 'δ10, %', 'окалина, %', 'Изгиб',
       'осадка', 'перегиб', 'ПРИМ', 'ДАТА', 'ЦЕЛЬ', 'оператор', 'протокол',
       '№заказа', 'σв, МПа', 'Ψ, %', 'окалина', 'овал', 'Бригада №',
       'Приложение по выплавке']]
df_wire_rod.rename(columns = {'ПЛАВКА' : '№ плавки'}, inplace = True)
df_wire_rod.rename(columns = {'ИМЯ' : '№ образца'}, inplace = True)
df_wire_rod.rename(columns = {'Unnamed: 1' : 'Линия проката'}, inplace = True)
df_wire_rod.rename(columns = {'Unnamed: 3' : 'Начало/конец образца'}, inplace = True)

Избавляемся от незаполненных № плавок и неправильнозаполненных значений в DF катанки.

df_wire_rod.loc[(df_wire_rod['№ плавки'] == "222160*9"), '№ плавки'] = "-"
df_wire_rod = df_wire_rod.loc[df_wire_rod['№ плавки'] != "-"]

Так как для дальнейших манипуляций необходимо изменить размерность данных, указываем интрересующим нам столбцам верные форматы данных.

df_wire_rod = df_wire_rod.astype({'№ плавки' : 'int64'})
df_wire_rod = df_wire_rod.astype({'Unnamed: 4' : 'object'})

Удаляем столбец № плавки и заменяем индекс столбцом № плавки.

df_melt.set_index('№ плавки', drop=True, append=False, inplace=True, verify_integrity=False)

Выполняем слияние 2-х DF плавки и катанки.

df_melt_df_wire_rod = df_wire_rod.join(df_melt, on="№ плавки")
df_melt_df_wire_rod = pd.merge(df_wire_rod, df_melt, left_on="№ плавки", right_index=True, how="left", sort=False)

Проверяем адекватность значений в столбце СТАЛЬ, на предмет правильности заполнения.

df_melt_df_wire_rod['СТАЛЬ'].unique()

Выводим в отдельный DF данные с значением 15Гпс в столбце СТАЛЬ.

df_melt_df_wire_rod_15Gps = df_melt_df_wire_rod[df_melt_df_wire_rod['СТАЛЬ'] == '15Гпс']

Проверяем адекватность введения значений в столбце Диаметр проволоки.

df_melt_df_wire_rod['Dном, мм'].unique()

Выводим в отдельный DF данные с значением 15Гпс в столбце СТАЛЬ и данные с значением 5.5 в столбце Dном, мм.

df_melt_df_wire_rod_15Gps_5_5_diam = df_melt_df_wire_rod_15Gps[df_melt_df_wire_rod_15Gps['Dном, мм'] == 5.5]

Исключаем значения - и изменяем формат данных.

df_melt_df_wire_rod_15Gps_5_5_diam = df_melt_df_wire_rod_15Gps_5_5_diam.loc[df_melt_df_wire_rod_15Gps_5_5_diam['σв, Н/мм2'] != "-"]
df_melt_df_wire_rod_15Gps_5_5_diam = df_melt_df_wire_rod_15Gps_5_5_diam.astype({'σв, Н/мм2' : 'float64'})
df_melt_df_wire_rod_15Gps_5_5_diam = df_melt_df_wire_rod_15Gps_5_5_diam.loc[df_melt_df_wire_rod_15Gps_5_5_diam['Ψ,%'] != "-"]
df_melt_df_wire_rod_15Gps_5_5_diam = df_melt_df_wire_rod_15Gps_5_5_diam.astype({'Ψ,%' : 'float64'})

Удаляем значения строки без химического состава стали по признаку: удалить все строки в столбце 'C', без данных в этом стотблце плавка стсали не существует.

df_melt_df_wire_rod_15Gps_5_5_diam = df_melt_df_wire_rod_15Gps_5_5_diam.dropna(axis=0, how='any', subset=['C'])

Проверяем ковариацию двух столбцов, а именно механического свойсвта: временного сопротивления разрыву проволоки и химического элемента: С (углерод).

np.cov(df_melt_df_wire_rod_15Gps_5_5_diam['σв, Н/мм2'],df_melt_df_wire_rod_15Gps_5_5_diam['C'])

Описательная статистика полученного DF.

df_melt_df_wire_rod_15Gps_5_5_diam.describe()

Проверка попарной корреляции всех столбцов с столбцом 'σв, Н/мм2'.

df_melt_df_wire_rod_15Gps_5_5_diam.corrwith(df_melt_df_wire_rod_15Gps_5_5_diam['σв, Н/мм2'])

Проверка попарной корреляции всех столбцов с столбцом 'Ψ,%'.

df_melt_df_wire_rod_15Gps_5_5_diam.corrwith(df_melt_df_wire_rod_15Gps_5_5_diam['Ψ,%'])

Строим график зависимости 'σв, Н/мм2' от 'Si, %'.

import matplotlib.pyplot as plt
plt.scatter(df_melt_df_wire_rod_15Gps_5_5_diam['Si'] ,df_melt_df_wire_rod_15Gps_5_5_diam['σв, Н/мм2'] , color = 'r')
plt.xlabel('Si, %')
plt.ylabel('σв, Н/мм2')

Построим тепловую карту наиболее интересующих нас (согласно теории выплавки).

cols = ['σв, Н/мм2', 'Ψ,%', 'Si', 'Ni', 'Sn', 'Nb']
sns.heatmap(df_melt_df_wire_rod_15Gps_5_5_diam[cols].corr(),
            cbar=True,
            annot=True)

Построим линейную регрессию зависимости 'σв, Н/мм2' от уровня содержания 'Si' (кремния).

cols = ['σв, Н/мм2', 'Si']
df_LR = df_melt_df_wire_rod_15Gps_5_5_diam[cols]
df_LR.shape
X = df_LR[['Si']]
y = df_LR[['σв, Н/мм2']]
reg = LinearRegression().fit(X, y)
plt.scatter(X, y, color = 'red')
plt.plot(X, reg.predict(X), color = 'blue')
plt.xlabel(cols[-1:])
plt.ylabel(cols[:-1])
plt.show()
