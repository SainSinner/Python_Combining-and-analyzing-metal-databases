# Python_Combining and analyzing metal databases
 Consolidation of databases and analysis of the effect of melt composition on the mechanical properties of rolled products.

На предприятии существует проблема связанная с длительностью анализа влияния факторов производства на свойства конечного изделия.

Сейчас информация о испытаниях разных переделов ведется в разных таблицах Excel, что увеличивает время анализа.

Озадачившись вопросом: каким образом химический состав плавки влияет на механические свойства? 

Первым делом приступил к структурированию первой формы таблицы в которой указаны номера плавок и их химические составы.

Изначально данные о плавках были разделены по месяцам на листы файла и представлены в следующем виде.

![image](https://user-images.githubusercontent.com/35117005/192804766-1bb3fdb9-3c74-4a48-84e9-b79381229fea.png)

Для адекватного отображения названия столбцов применил следующее переименование для всех рассматриваемых месяцв.

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

Так как для дальнейших манипуляций необходимо изменить размерность данных, указываем интрересующим нам столбцам верные размерности.

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

