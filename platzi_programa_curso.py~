#!coding: utf-8
# author: pjmakey2@gmail.com
# program: Scrap platzi clases to make a excel report
# purpose: Present a program of training for the tech staff in a business company
import pandas as pd
import requests
from bs4 import BeautifulSoup
mpath = 'https://platzi.com/clases'
#list of url clases
urls = [
    '{}/terminal/'.format(mpath),
    '{}/bash-shell/'.format(mpath),
    '{}/sql-mysql/'.format(mpath),
    '{}/postgresql/'.format(mpath),
    '{}/python-2019/'.format(mpath),
    '{}/intro-selenium/'.format(mpath),
    '{}/django/'.format(mpath),
    '{}/django-avanzado/'.format(mpath),
    '{}/celery/'.format(mpath),
    '{}/git-github/'.format(mpath),
    '{}/html5-css3/'.format(mpath),
    '{}/responsive-design/'.format(mpath),
    '{}/css-grid-layout/'.format(mpath),
    '{}/bootstrap/'.format(mpath),
    '{}/javascript-navegador/'.format(mpath),
    '{}/javascript-jquery-2015/'.format(mpath),
    '{}/jquery-js/'.format(mpath),
    '{}/javascript-profesional/'.format(mpath),
    '{}/react/'.format(mpath),
    '{}/ecmascript-6/'.format(mpath),
    '{}/react-router/'.format(mpath),
    '{}/react-ejs/'.format(mpath),
    '{}/redux/'.format(mpath),
    '{}/react-router-redux/'.format(mpath),
    '{}/react-avanzado/'.format(mpath),
    '{}/react-hooks/'.format(mpath),
    '{}/pandas/'.format(mpath),
    '{}/react-native/'.format(mpath),
    '{}/react-native-old/'.format(mpath),
    '{}/react-navigation/'.format(mpath),
]
#const metrics
hsecs = 3600
dhsecs = 0.016667
#map hours
horas = {
    '1 horas': 1*hsecs,
    '2 horas': 2*hsecs,
    '3 horas': 3*hsecs,
    '4 horas': 4*hsecs,
    '5 horas': 5*hsecs,
    '6 horas': 6*hsecs,
    '7 horas': 7*hsecs,
    '8 horas': 8*hsecs,
    '9 horas': 9*hsecs,
    '10 horas':10*hsecs,
    '11 horas':11*hsecs,
    '12 horas':12*hsecs,
    '13 horas':13*hsecs,
    '14 horas':14*hsecs,
    '15 horas':15*hsecs,
    0:0
}

def compose_hours(minutes, seconds):
    tsecs = minutes/dhsecs
    mi, se = divmod(tsecs,60)
    ho, mi = divmod(m, 60)
    tt = '{:0.0f}:{:0.0f}:{:0.0f}'.format(ho,mi,seconds)
    ffs = '%H:%M:%S'
    return tt, ffs

def colect_platzi_class_info(order, url):
    data = requests.get(url)
    soup = BeautifulSoup(data.text, 'html.parser')
    titulo = soup.find('h1', attrs={'data-reactid': '11'})
    duracion = soup.find('span', attrs={'data-reactid': '30'})
    duracion = duracion.text.replace(' de contenido', '')
    materials = soup.find_all(class_='Material-concept')
    ndata = []
    tmpobj = {
        'order': order,
        'suborder': order,
        'curso': titulo.text,
        'duracion_total': duracion,
        'titulo_bloque': titulo.text,
        'segmento': titulo.text,
        'duracion_segmento': 0
    }
    ndata.append(tmpobj)
    for idxr, mm in enumerate(materials):
        titulo_bloque = mm.find(class_='Material-title').text
        segs = mm.find_all('p', class_='MaterialItem-copy-title')
        times = mm.find_all('p', class_='MaterialItem-copy-time')
        for idxs,(s, t) in enumerate(zip(segs, times)):
            idxs = idxs+1
            tt = t.text.replace(' min', '')            
            minutes = int(tt.split(':')[0])
            seconds = int(tt.split(':')[-1])
            ffs = '%M:%S'
            if minutes >= 60:
                tt, ffs = compose_hours(minutes, seconds)
            c = datetime.datetime.strptime(tt, ffs)
            a_timedelta = c - datetime.datetime(1900, 1, 1)
            seconds = a_timedelta.total_seconds()
            tmpobj = {
                'order': order,
                'suborder': order+idxr+idxs,
                'curso': titulo.text,
                'duracion_total': 0,
                'titulo_bloque': titulo_bloque,
                'segmento': s.text,
                'duracion_segmento': seconds,
            }        
            ndata.append(tmpobj)
    df = pd.DataFrame(ndata)
    df['duracion_total'] = df['duracion_total'].map(horas)
    return df
dfs = []
for idx, url in enumerate(urls):
    dfs.append(colect_platzi_class_info(idx*10, url))
df = pd.concat(dfs)
scols = ['order','suborder', 'curso','duracion_total','titulo_bloque','segmento','duracion_segmento']
#Save the data
df[scols].to_csv('/tmp/curso_metricas_scrappy.csv', sep='|', quotechar='"', encoding='utf-8', index=False, header=True)
def readable_times(time):
    day = time // (24 * 3600)
    time = time % (24 * 3600)
    hour = time // 3600
    time %= 3600
    minutes = time // 60
    time %= 60
    seconds = time
    return "{:0.0f} {:0.0f}:{:0.0f}:{:0.0f}".format(day, hour, minutes, seconds)
df = pd.read_csv('/tmp/curso_metricas_scrappy.csv', sep='|', quotechar='"', encoding='utf-8')
df['duracion_read'] =  '0'
mark = df['duracion_total'] > 0
df.loc[mark, 'duracion_read' ] = df[mark]['duracion_total'].apply(readable_times)
mark = df['duracion_segmento'] > 0
df.loc[mark, 'duracion_read' ] = df[mark]['duracion_segmento'].apply(readable_times)
dfa = pd.pivot_table(df, index=['order', 'curso', 'suborder','titulo_bloque', 'segmento'],
                           values=['duracion_read'],
                           aggfunc=['sum'], fill_value=0)
dfa.to_excel('/tmp/programa_capacitaion_tecnologia.xlsx')

