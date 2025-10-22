import streamlit as st
import pandas as pd
import gspread
from google.oauth2.service_account import Credentials
from datetime import datetime

# Configuração das permissões do Google Sheets
scope = ["https://www.googleapis.com/robot/v1/metadata/x509/vota-o-phayton%40firm-mariner-397622.iam.gserviceaccount.com",
         "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url"]

# 🔹 Substitua pelo conteúdo do seu arquivo JSON de credenciais (do Google Cloud)
credentials = Credentials.from_service_account_info(st.secrets["gcp_service_account"], scopes=scope)
client = gspread.authorize(credentials)

# 🔹 Substitua pelo link da sua planilha
SHEET_URL = "(https://docs.google.com/spreadsheets/d/1WTrj66uhcrhjT1XR5rJlZ9MWC-V30N10Hr6YdJXOLBo/edit?usp=sharing)"
sheet = client.open_by_url(SHEET_URL)
aba_candidatos = sheet.worksheet("Candidatos")
aba_votos = sheet.worksheet("Votos")

# 🔹 Leitura dos nomes dos candidatos
candidatos = aba_candidatos.col_values(1)[1:]  # Ignora o cabeçalho

st.title("🗳️ Formulário de Votação")
st.write("Escolha o seu candidato abaixo:")

escolha = st.selectbox("Selecione seu candidato", candidatos)
if st.button("Votar"):
    aba_votos.append_row([escolha, str(datetime.now())])
    st.success("✅ Voto registrado com sucesso!")
