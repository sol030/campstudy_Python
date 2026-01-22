```python
##기존 데이터
import pandas as pd

df = pd.DataFrame({
    "order_id": ["O001","O002","O003","O004","O005","O006"],
    "date": ["2026-01-01","2026-01-02","2026-01-03","2026-01-04","2026-01-05","2026-01-06"],
    "store": ["A","A","B","A","B","A"],
    "menu": ["Latte","Americano","Mocha","Latte","Americano","Mocha"],
    "price": [5000,4500,5500,5000,4500,5500],
    "qty": [1,2,1,3,1,2],
    "paid": [True, True, False, True, True, False]
})

df["date"] = pd.to_datetime(df["date"])
df

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>date</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>O001</td>
      <td>2026-01-01</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>O002</td>
      <td>2026-01-02</td>
      <td>A</td>
      <td>Americano</td>
      <td>4500</td>
      <td>2</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>O003</td>
      <td>2026-01-03</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>O004</td>
      <td>2026-01-04</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>3</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>O005</td>
      <td>2026-01-05</td>
      <td>B</td>
      <td>Americano</td>
      <td>4500</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>5</th>
      <td>O006</td>
      <td>2026-01-06</td>
      <td>A</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>2</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>




```python
#####데이터 확장
#customer_id 추가 (재구매 구조 만들기)
df["customer_id"] = ["C01","C01","C02","C01","C02","C03"]

#C01 → 여러 번 주문
#C02 → 여러 번 주
#C03 → 1회성 고객
```


```python
#날짜를 여러 달로 분산
df.loc[df["order_id"] == "O004", "date"] = "2026-02-10"
df.loc[df["order_id"] == "O005", "date"] = "2026-02-18"
df.loc[df["order_id"] == "O006", "date"] = "2026-03-05"

df["date"] = pd.to_datetime(df["date"])
df

#이제 재구매 + 시간 경과 구조 OK
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>date</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
      <th>customer_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>O001</td>
      <td>2026-01-01</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
      <td>C01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>O002</td>
      <td>2026-01-02</td>
      <td>A</td>
      <td>Americano</td>
      <td>4500</td>
      <td>2</td>
      <td>True</td>
      <td>C01</td>
    </tr>
    <tr>
      <th>2</th>
      <td>O003</td>
      <td>2026-01-03</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>1</td>
      <td>False</td>
      <td>C02</td>
    </tr>
    <tr>
      <th>3</th>
      <td>O004</td>
      <td>2026-02-10</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>3</td>
      <td>True</td>
      <td>C01</td>
    </tr>
    <tr>
      <th>4</th>
      <td>O005</td>
      <td>2026-02-18</td>
      <td>B</td>
      <td>Americano</td>
      <td>4500</td>
      <td>1</td>
      <td>True</td>
      <td>C02</td>
    </tr>
    <tr>
      <th>5</th>
      <td>O006</td>
      <td>2026-03-05</td>
      <td>A</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>2</td>
      <td>False</td>
      <td>C03</td>
    </tr>
  </tbody>
</table>
</div>




```python
###코호트 분석 시작
#주문 월 컬럼 생성
df["order_month"] = df["date"].dt.to_period("M")

```


```python
#고객별 첫 방문 월 (코호트 기준)
df["first_visit_month"] = (
    df.groupby("customer_id")["order_month"]
      .transform("min")
)

#이게 코호트 그룹
```


```python
###코호트 집계 테이블 만들기
#코호트 x 주문월별 고객수

cohort_data = (
    df.groupby(["first_visit_month", "order_month"])
      .agg(n_customers=("customer_id", "nunique"))
      .reset_index()
)

cohort_data


```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>first_visit_month</th>
      <th>order_month</th>
      <th>n_customers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01</td>
      <td>2026-01</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026-01</td>
      <td>2026-02</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2026-03</td>
      <td>2026-03</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
#기간 번호 계산 (n개월 차)
cohort_data["period_number"] = (
    cohort_data["order_month"] - cohort_data["first_visit_month"]
).apply(lambda x: x.n)

cohort_data
#0 = 첫 구매 월
#1 = 1개월 뒤
#2 = 2개월 뒤
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>first_visit_month</th>
      <th>order_month</th>
      <th>n_customers</th>
      <th>period_number</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01</td>
      <td>2026-01</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026-01</td>
      <td>2026-02</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2026-03</td>
      <td>2026-03</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
##코호트 피벗 테이블 (최종 결과)
cohort_pivot = cohort_data.pivot_table(
    index="first_visit_month",
    columns="period_number",
    values="n_customers"
)

cohort_pivot

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>period_number</th>
      <th>0</th>
      <th>1</th>
    </tr>
    <tr>
      <th>first_visit_month</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2026-01</th>
      <td>2.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>2026-03</th>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
#### +++ 리텐션 비율로 바꾸기
retention = cohort_pivot.divide(cohort_pivot[0], axis=0)    #n개월차 인원 / 첫 달 인원
retention

#첫 달 대비 몇 %가 남았지?
## -> 각 코호트의 첫 방문자 수를 100%로 놓고
## -> n개월 후에도 살아있는 비율을 계산한 것
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>period_number</th>
      <th>0</th>
      <th>1</th>
    </tr>
    <tr>
      <th>first_visit_month</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2026-01</th>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2026-03</th>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
########## 시각화까지?!?!?
import seaborn as sns
import matplotlib.pyplot as plt

# 1. 한글 깨짐 방지 설정 (필요 시)
plt.rc('font', family='Malgun Gothic') # Windows
# plt.rc('font', family='AppleGothic') # Mac
plt.rcParams['axes.unicode_minus'] = False

# 2. 히트맵 그리기
plt.figure(figsize=(12, 8))
sns.heatmap(retention, 
            annot=True,          # 숫자 표시
            fmt='.0%',           # 백분율 형식
            cmap='YlGnBu',       # 노랑-초록-파랑 색상 조합
            cbar_kws={'label': 'Retention Rate'})

plt.title('카페 고객 코호트 리텐션 분석', fontsize=16, pad=20)
plt.xlabel('경과 월 (Month)', fontsize=12)
plt.ylabel('방문 코호트 (First Visit)', fontsize=12)

plt.show()
```


    
![png](03_01_cafe_data_cohort_files/03_01_cafe_data_cohort_9_0.png)
    



```python
print("sonamu")
```

    sonamu
    


```python
#!python -m jupyter nbconvert --to markdown D:\sonamu\campstudy_Python\titanic_lab\cafe_lab\03_01_cafe_data_cohort.ipynb
```

    This application is used to convert notebook files (*.ipynb)
            to various other formats.
    
            WARNING: THE COMMANDLINE INTERFACE MAY CHANGE IN FUTURE RELEASES.
    
    Options
    =======
    The options below are convenience aliases to configurable class-options,
    as listed in the "Equivalent to" description-line of the aliases.
    To see all configurable class-options for some <cmd>, use:
        <cmd> --help-all
    
    --debug
        set log level to logging.DEBUG (maximize logging output)
        Equivalent to: [--Application.log_level=10]
    --show-config
        Show the application's configuration (human-readable format)
        Equivalent to: [--Application.show_config=True]
    --show-config-json
        Show the application's configuration (json format)
        Equivalent to: [--Application.show_config_json=True]
    --generate-config
        generate default config file
        Equivalent to: [--JupyterApp.generate_config=True]
    -y
        Answer yes to any questions instead of prompting.
        Equivalent to: [--JupyterApp.answer_yes=True]
    --execute
        Execute the notebook prior to export.
        Equivalent to: [--ExecutePreprocessor.enabled=True]
    --allow-errors
        Continue notebook execution even if one of the cells throws an error and include the error message in the cell output (the default behaviour is to abort conversion). This flag is only relevant if '--execute' was specified, too.
        Equivalent to: [--ExecutePreprocessor.allow_errors=True]
    --stdin
        read a single notebook file from stdin. Write the resulting notebook with default basename 'notebook.*'
        Equivalent to: [--NbConvertApp.from_stdin=True]
    --stdout
        Write notebook output to stdout instead of files.
        Equivalent to: [--NbConvertApp.writer_class=StdoutWriter]
    --inplace
        Run nbconvert in place, overwriting the existing notebook (only
                relevant when converting to notebook format)
        Equivalent to: [--NbConvertApp.use_output_suffix=False --NbConvertApp.export_format=notebook --FilesWriter.build_directory=]
    --clear-output
        Clear output of current file and save in place,
                overwriting the existing notebook.
        Equivalent to: [--NbConvertApp.use_output_suffix=False --NbConvertApp.export_format=notebook --FilesWriter.build_directory= --ClearOutputPreprocessor.enabled=True]
    --coalesce-streams
        Coalesce consecutive stdout and stderr outputs into one stream (within each cell).
        Equivalent to: [--NbConvertApp.use_output_suffix=False --NbConvertApp.export_format=notebook --FilesWriter.build_directory= --CoalesceStreamsPreprocessor.enabled=True]
    --no-prompt
        Exclude input and output prompts from converted document.
        Equivalent to: [--TemplateExporter.exclude_input_prompt=True --TemplateExporter.exclude_output_prompt=True]
    --no-input
        Exclude input cells and output prompts from converted document.
                This mode is ideal for generating code-free reports.
        Equivalent to: [--TemplateExporter.exclude_output_prompt=True --TemplateExporter.exclude_input=True --TemplateExporter.exclude_input_prompt=True]
    --allow-chromium-download
        Whether to allow downloading chromium if no suitable version is found on the system.
        Equivalent to: [--WebPDFExporter.allow_chromium_download=True]
    --disable-chromium-sandbox
        Disable chromium security sandbox when converting to PDF..
        Equivalent to: [--WebPDFExporter.disable_sandbox=True]
    --show-input
        Shows code input. This flag is only useful for dejavu users.
        Equivalent to: [--TemplateExporter.exclude_input=False]
    --embed-images
        Embed the images as base64 dataurls in the output. This flag is only useful for the HTML/WebPDF/Slides exports.
        Equivalent to: [--HTMLExporter.embed_images=True]
    --sanitize-html
        Whether the HTML in Markdown cells and cell outputs should be sanitized..
        Equivalent to: [--HTMLExporter.sanitize_html=True]
    --log-level=<Enum>
        Set the log level by value or name.
        Choices: any of [0, 10, 20, 30, 40, 50, 'DEBUG', 'INFO', 'WARN', 'ERROR', 'CRITICAL']
        Default: 30
        Equivalent to: [--Application.log_level]
    --config=<Unicode>
        Full path of a config file.
        Default: ''
        Equivalent to: [--JupyterApp.config_file]
    --to=<Unicode>
        The export format to be used, either one of the built-in formats
                ['asciidoc', 'custom', 'html', 'latex', 'markdown', 'notebook', 'pdf', 'python', 'qtpdf', 'qtpng', 'rst', 'script', 'slides', 'webpdf']
                or a dotted object name that represents the import path for an
                ``Exporter`` class
        Default: ''
        Equivalent to: [--NbConvertApp.export_format]
    --template=<Unicode>
        Name of the template to use
        Default: ''
        Equivalent to: [--TemplateExporter.template_name]
    --template-file=<Unicode>
        Name of the template file to use
        Default: None
        Equivalent to: [--TemplateExporter.template_file]
    --theme=<Unicode>
        Template specific theme(e.g. the name of a JupyterLab CSS theme distributed
        as prebuilt extension for the lab template)
        Default: 'light'
        Equivalent to: [--HTMLExporter.theme]
    --sanitize_html=<Bool>
        Whether the HTML in Markdown cells and cell outputs should be sanitized.This
        should be set to True by nbviewer or similar tools.
        Default: False
        Equivalent to: [--HTMLExporter.sanitize_html]
    --writer=<DottedObjectName>
        Writer class used to write the
                                            results of the conversion
        Default: 'FilesWriter'
        Equivalent to: [--NbConvertApp.writer_class]
    --post=<DottedOrNone>
        PostProcessor class used to write the
                                            results of the conversion
        Default: ''
        Equivalent to: [--NbConvertApp.postprocessor_class]
    --output=<Unicode>
        Overwrite base name use for output files.
                    Supports pattern replacements '{notebook_name}'.
        Default: '{notebook_name}'
        Equivalent to: [--NbConvertApp.output_base]
    --output-dir=<Unicode>
        Directory to write output(s) to. Defaults
                                      to output to the directory of each notebook. To recover
                                      previous default behaviour (outputting to the current
                                      working directory) use . as the flag value.
        Default: ''
        Equivalent to: [--FilesWriter.build_directory]
    --reveal-prefix=<Unicode>
        The URL prefix for reveal.js (version 3.x).
                This defaults to the reveal CDN, but can be any url pointing to a copy
                of reveal.js.
                For speaker notes to work, this must be a relative path to a local
                copy of reveal.js: e.g., "reveal.js".
                If a relative path is given, it must be a subdirectory of the
                current directory (from which the server is run).
                See the usage documentation
                (https://nbconvert.readthedocs.io/en/latest/usage.html#reveal-js-html-slideshow)
                for more details.
        Default: ''
        Equivalent to: [--SlidesExporter.reveal_url_prefix]
    --nbformat=<Enum>
        The nbformat version to write.
                Use this to downgrade notebooks.
        Choices: any of [1, 2, 3, 4]
        Default: 4
        Equivalent to: [--NotebookExporter.nbformat_version]
    
    Examples
    --------
    
        The simplest way to use nbconvert is
    
                > jupyter nbconvert mynotebook.ipynb --to html
    
                Options include ['asciidoc', 'custom', 'html', 'latex', 'markdown', 'notebook', 'pdf', 'python', 'qtpdf', 'qtpng', 'rst', 'script', 'slides', 'webpdf'].
    
                > jupyter nbconvert --to latex mynotebook.ipynb
    
                Both HTML and LaTeX support multiple output templates. LaTeX includes
                'base', 'article' and 'report'.  HTML includes 'basic', 'lab' and
                'classic'. You can specify the flavor of the format used.
    
                > jupyter nbconvert --to html --template lab mynotebook.ipynb
    
                You can also pipe the output to stdout, rather than a file
    
                > jupyter nbconvert mynotebook.ipynb --stdout
    
                PDF is generated via latex
    
                > jupyter nbconvert mynotebook.ipynb --to pdf
    
                You can get (and serve) a Reveal.js-powered slideshow
    
                > jupyter nbconvert myslides.ipynb --to slides --post serve
    
                Multiple notebooks can be given at the command line in a couple of
                different ways:
    
                > jupyter nbconvert notebook*.ipynb
                > jupyter nbconvert notebook1.ipynb notebook2.ipynb
    
                or you can specify the notebooks list in a config file, containing::
    
                    c.NbConvertApp.notebooks = ["my_notebook.ipynb"]
    
                > jupyter nbconvert --config mycfg.py
    
    To see all available configurables, use `--help-all`.
    
    

    [NbConvertApp] WARNING | pattern 'D:\\sonamu\\campstudy_Python\\titanic_lab\\cafe_lab\\03_cafe_data_cohort.ipynb' matched no files
    


```python
!python -m jupyter nbconvert --to markdown 03_01_cafe_data_cohort.ipynb
```
