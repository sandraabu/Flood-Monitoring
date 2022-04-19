import dash
import dash_bootstrap_components as dbc
import dash_html_components as html
import requests
import pandas as pd
import dash_core_components as dcc
import plotly.express as px
import numpy as np
from dash.dependencies import Input,Output
import dash_table

app = dash.Dash(external_stylesheets = [ dbc.themes.JOURNAL],)


RIVER_IMG = "https://www.publicdomainpictures.net/pictures/160000/velka/river-145642059951y.jpg"


url = "http://environment.data.gov.uk/flood-monitoring/id/floods"
r_ = requests.get(url=url)
json_ = r_.json()
data = json_['items']
df = pd.DataFrame.from_dict(data, orient='columns')
df = pd.concat([df, df["floodArea"].apply(pd.Series)], axis=1)
list = ['id_1', 'description','area_name','region_name','flood_area','flood_area_id', 'is_tidal', 'message', 
        'severity', 'severity_level', 'time_message_changed', 'time_raised', 'time_severity_changed', 'id_2', 
        'county','notation','polygon','river_or_sea']
df.columns = list
df.drop(columns=['region_name', 'flood_area', 'id_2'], inplace = True)




description = df['description'][0]
area_name = df['area_name'][0]
county = df['county'][0]
severity_level = df['severity_level'][0]
message = df['message'][0]
time_changed = df['time_message_changed'][0]


from urllib.request import urlopen
import json
with urlopen('https://environment.data.gov.uk/flood-monitoring/id/floodAreas.json') as response:
    counties = json.load(response)



def world_map(df,geojson):
    
    fig=px.choropleth(df,
             geojson='http://environment.data.gov.uk/flood-monitoring/id/floodAreas/112WATSOM1/polygon.json', 
             featureidkey='properties.AREA',   
             locations='area_name',        #column in dataframe
              #animation_frame='Year',       #dataframe
              color='severity_level',  #dataframe
              color_continuous_scale='Inferno',
               title='Severity level' ,  
               height=700
              )
    fig.update_geos(fitbounds="locations", visible=False)
    fig.show()
    return fig




body_app = dbc.Container([

    dbc.Row( html.Marquee("This uses Environment Agency flood and river level data from the real-time data API (Beta)"), style = {'color':'red'}),

    dbc.Row([
#         dbc.Col(dbc.Card(data_for_cases("Severity level",f'{severity_level:,}',f'{severity_level:,}' ), color = 'primary',style = {'text-align':'center'}, inverse = True),xs = 12, sm = 12, md = 4, lg = 4, xl = 4, style = {'padding':'12px 12px 12px 12px'}),
#         dbc.Col(dbc.Card(data_for_cases("Recovered",f'{area_name:,}',f'{area_name:,}' ), color = 'success',style = {'text-align':'center'}, inverse = True),xs = 12, sm = 12, md = 4, lg = 4, xl = 4, style = {'padding':'12px 12px 12px 12px'})
#         dbc.Col(dbc.Card(data_for_cases("Deaths",f'{deaths:,}',f'{newdeaths:,}' ), color = 'danger',style = {'text-align':'center'}, inverse = True),xs = 12, sm = 12, md = 4, lg = 4, xl = 4, style = {'padding':'12px 12px 12px 12px'})


        ]),

    html.Br(),
    html.Br(),

#     dbc.Row([html.Div(html.H4('UK Flood Monitoring'),
#                       style = {'textAlign':'center','fontWeight':'bold','family':'georgia','width':'100%', 'color': 'red'})]),

#     html.Br(),
#     html.Br(),

    dbc.Row([
             #dbc.Col(dcc.Graph(id = 'world-graph', figure = world_map(df,geojson='http://environment.data.gov.uk/flood-monitoring/id/floodAreas/112WATSOM1/polygon.json')),style = {'height':'450px'},xs = 12, sm = 12, md = 6, lg = 6, xl = 6),

             dbc.Col([html.Div(id = 'dropdown-div', children =
             [dcc.Dropdown(id = 'county-dropdown',
                 options = [{'label':i, 'value':i} for i in np.append(['All'],df['county'].unique()) ],
                 value = 'All',
                 placeholder = 'Select the county'
                 )], style = {'width':'100%', 'display':'inline-block'}),

                      html.Div(id = 'flood-table-output')
                      ],style = {'width':'150%','height':'450px','text-align':'center'},xs = 12, sm = 12, md = 6, lg = 6, xl = 6)

                     ])




     ],fluid = True)

navbar = dbc.Navbar( id = 'navbar', children = [


    html.A(
    dbc.Row([
        dbc.Col(html.Img(src = RIVER_IMG, height = "150px")),
        dbc.Col(
            dbc.NavbarBrand("UK FLOOD MONITORING", style = {'textAlign':'center','color':'red', 'fontSize':'35px','fontFamily':'woff2'}
                            )

            )


        ],align = "center",
        # no_gutters = True
        ),
    href = '/'
    ),
    
                dbc.Row(
            [
        dbc.Col(
             dbc.Button(id = 'button', children = "Real Time Data API(Beta)", color = "primary", className = 'ml-auto', href = 'https://environment.data.gov.uk/flood-monitoring/doc/reference')

            )        
    ],
     className="g-0 ms-auto flex-nowrap mt-3 mt-md-0",
)
    ])
   



app.layout = html.Div(id = 'parent', children = [navbar, body_app])



@app.callback(Output(component_id='flood-table-output', component_property= 'children'),
              [Input(component_id='county-dropdown', component_property='value')])

def table_county(county):
    if county == 'All':
        df_final = df
    else:
        df_final = df.loc[df['county'] == '{}'.format(county)]

    return dash_table.DataTable(
    data = df_final[['county','description','message','severity_level','time_message_changed']].to_dict('records'),
    columns = [{'id':c , 'name':c} for c in df_final[['county','description','message','severity_level','time_message_changed']].columns],
    fixed_rows = {'headers':True},

    sort_action = 'native',

    style_table = {'overflowX': 'auto'
                   },

    style_header = {'backgroundColor':'rgb(224,224,224)',
                    'fontWeight':'bold',
                    'border':'4px solid white',
                    'fontSize':'12px'
                    },

    style_data_conditional = [

              {
                'if': {'row_index': 'odd',
                       #'column_id': 'ratio',
                       },
                'backgroundColor': 'rgb(240,240,240)',
                'fontSize':'12px',
                },

            {
                  'if': {'row_index': 'even'},
                  'backgroundColor': 'rgb(255, 255, 255)',
                'fontSize':'12px',

            }

        ],

    style_cell = {
        'textAlign':'centre',
        'fontFamily':'woff2',
        #'border':'4px solid white',
        
        'height': 'auto',
        'minWidth': '180px', 
#         'width': '180px', 
         'maxWidth': '180px',
        'whiteSpace': 'normal',
         #'maxWidth': 0,
        #'whiteSpace':'normal',
        # 'overflow':'hidden',
        'textOverflow': 'ellipsis',



        },

     

  )



if __name__ == "__main__":
    app.run_server()
