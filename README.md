# leitura-de-trafego-http-https

Passo a passo para analisar os pacotes HTTP no **Pandas** e gerar gráficos com **Matplotlib/Seaborn**.

```python
pip install pandas matplotlib

```

---

### **1. Carregar o arquivo CSV no Pandas**
```python
import pandas as pd

# Carregar o arquivo CSV
file_path = "/content/captura.csv"
df = pd.read_csv(file_path)

# Visualizar as primeiras linhas para entender a estrutura
print(df.head())
```
Isso permite identificar colunas relevantes, como **Protocol**, **Info**, **Source**, **Destination**, etc.

---

### **2. Filtrar Pacotes HTTP e HTTPS**
O tráfego HTTP geralmente ocorre nas portas **80** e **443** (HTTPS). Para filtrar:

```python
# Filtrar pacotes HTTP (porta 80) e HTTPS (porta 443)
df_http = df[df["Protocol"] == "HTTP"]
df_https = df[df["Protocol"] == "HTTPS"]
```

Se sua captura não tiver uma coluna específica para HTTP, tente buscar por palavras-chave em **Info**:
```python
df_http = df[df["Info"].str.contains("80", na=False)]
df_https = df[df["Info"].str.contains("443", na=False)]
```

---

### **3. Contar Domínios Visitados**
Se a coluna **Info** contiver URLs, podemos extrair os domínios:

```python
# Extrair domínios visitados
dominios_visitados = df_http["Info"].dropna().unique()

# Exibir os primeiros 10 domínios
print(dominios_visitados[:10])
```

Para ver quais foram os mais acessados:
```python
# Contar ocorrências de domínios
df_http["Info"].value_counts().head(10)
```

---

### **4. Contar Solicitações HTTP e HTTPS**
```python
# Quantidade de solicitações HTTP e HTTPS
solicitacoes_http = len(df_http)
solicitacoes_https = len(df_https)

print(f"Solicitações HTTP: {solicitacoes_http}")
print(f"Solicitações HTTPS: {solicitacoes_https}")
```

---

### **5. Gerar Gráficos**
Agora, vamos criar gráficos para visualizar os dados.

```python
import matplotlib.pyplot as plt
import seaborn as sns

# Configurar o estilo dos gráficos
sns.set_theme(style="whitegrid")

# Criar um gráfico de barras para comparar HTTP x HTTPS
plt.figure(figsize=(6,4))
sns.barplot(x=["HTTP", "HTTPS"], y=[solicitacoes_http, solicitacoes_https], palette="Blues")
plt.title("Quantidade de Solicitações HTTP e HTTPS")
plt.ylabel("Número de Solicitações")
plt.show()
```

**Histograma para tempos de chegada de pacotes HTTP**
```python
plt.figure(figsize=(8,5))
sns.histplot(df_http["Time"].astype(float), bins=30, kde=True, color="green")
plt.title("Distribuição do Tempo de Chegada dos Pacotes HTTP")
plt.xlabel("Tempo (s)")
plt.ylabel("Frequência")
plt.show()
```

---
