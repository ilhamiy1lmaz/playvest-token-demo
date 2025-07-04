import streamlit as st

def hesapla_token_fiyati(
    eski_fiyat,
    gol=0,
    asist=0,
    dakikalik_oynama=90,
    sarikart=False,
    kirmizikart=False,
    penalti_kacirma=False,
    clean_sheet=False,
    pozisyon="orta_saha"
):
    if pozisyon == "forvet":
        gol_etki = 0.04 * gol
    elif pozisyon == "defans":
        gol_etki = 0.07 * gol
    else:
        gol_etki = 0.05 * gol

    asist_etki = 0.03 * asist
    dakikalar_etki = 0.01 if dakikalik_oynama >= 90 else 0.0
    clean_sheet_etki = 0.05 if clean_sheet else 0.0

    ceza_etki = 0.0
    if sarikart:
        ceza_etki -= 0.02
    if kirmizikart:
        ceza_etki -= 0.05
    if penalti_kacirma:
        ceza_etki -= 0.04

    toplam_etki = gol_etki + asist_etki + dakikalar_etki + clean_sheet_etki + ceza_etki
    yeni_fiyat = eski_fiyat * (1 + toplam_etki)
    return round(yeni_fiyat, 2)

st.title("Futbolcu Token Fiyat Hesaplayıcı")

eski_fiyat = st.number_input("Eski Token Fiyatı (₺)", min_value=0.0, value=10.0, step=0.1)
pozisyon = st.selectbox("Pozisyon", ["orta_saha", "forvet", "defans"])
gol = st.number_input("Gol Sayısı", min_value=0, step=1)
asist = st.number_input("Asist Sayısı", min_value=0, step=1)
dakikalik_oynama = st.slider("Oynanan Süre (dk)", min_value=0, max_value=90, value=90, step=1)
sarikart = st.checkbox("Sarı Kart")
kirmizikart = st.checkbox("Kırmızı Kart")
penalti_kacirma = st.checkbox("Penaltı Kaçırdı")
clean_sheet = st.checkbox("Kalesini Gole Kapadı (Kaleci/Defans)")

if st.button("Token Fiyatını Hesapla"):
    yeni_fiyat = hesapla_token_fiyati(
        eski_fiyat,
        gol,
        asist,
        dakikalik_oynama,
        sarikart,
        kirmizikart,
        penalti_kacirma,
        clean_sheet,
        pozisyon
    )
    st.success(f"Yeni Token Fiyatı: {yeni_fiyat} ₺")
