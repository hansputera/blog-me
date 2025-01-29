---
title: Dumping SNBT
description: Dump data siswa SNBT 2024 as free record history, does it work too in 2025?
draft: false
published: 2025-01-29
category: technology
image: https://i.imgur.com/swBYXM4.png
tags: [daily, technology, python, dumping, scraping, web]
---

# SNBT
Seleksi Nasional Berbasis Tes, tentunya teman-teman tidak asing bukan? Yap bener banget, SNBT baru aja diluncur beberapa tahun yang lalu.
Namun, temen-temen siapa nih yang kemarin sempet lolos SNBT 2024? Selamat ya 😄

## SNBT Dumper
Wait? What is that? SNBT Dumper? Yap, SNBT Dumper merupakan tool sederhana untuk melakukan dumping data dari situs kemendikbud untuk memperoleh data-data siswa siapa saja yang termasuk ke dalam SNBT.
Data yang diperoleh, meliputi No. UTBK, Nama, dan Kampus tujuan jika lolos. 

## What is purpose of SNBT Dumper?
The purpose of SNBT Dumper adalah untuk menyajikan data historikal secara terbuka dan open-source bagi semuanya yang ingin melihat data SNBT Indonesia.
The simply purpose is just "iseng" actually hahaha.

## Flow
Apabila kita merujuk pada situs cek pengumuman SNBT (https://pengumuman-snbt-snpmb.bppp.kemdikbud.go.id/), disini hanya terlihat kolomnya saja bukan?
![image](https://github.com/user-attachments/assets/4574bad0-f04e-4d1f-98e0-3d737313518c)
Namun, apabila kita menilik lebih lanjut pada inspect element/sources sambil melakukan input data yang random, kita dapat melihat error 404 pada Networks
![image](https://github.com/user-attachments/assets/95d57010-9d30-47c1-9e7b-067ec4e64a7b)
GYATT!!! We caught the googleapis storage url, terlihat menarik, coba kita kulik lebih dalam
![image](https://github.com/user-attachments/assets/d5e52ec9-521a-4a9a-aa96-18a63405e1d7)
Hmm?? Sama aja nih, coba lagi kita akses root pathnya
![image](https://github.com/user-attachments/assets/cb8024c9-e584-435a-b292-e5c59b9b2929)
Voilaa, we got entire SNBT users data heree.

## Technical
Oke lanjut nih, kita kan udah berhasil dapat datanya semua (hampir), gimana kita cara dapatinnya secara otomatis?
Berdasarkan kode yang ada pada sumber `snbt.min.js`, kita dapat melihat ada kode untuk fetching data `.dwg`, dan kemudian diolah ke UI.

Disini, saya ngga pake banyak tool, cuman runtime `Python 3.10` dengan dependencies `xmltodict`, `requests`, dan `sqlite3` aja.
Kurang lebih kodenya seperti dibawah ini,
```python3
import xmltodict
import csv
import requests
import sqlite3
from datetime import datetime
from config import config

current_timestamp = datetime.now().isoformat()

snbt_files = {
    'excel': f'snbt_dump_{current_timestamp}.csv', # CSV
    'db': f'snbt_dump_{current_timestamp}.db', # sqlite
}

"""
    Request data to SNBT GoogleApi Storage
"""
def request_data_all():
    response = requests.get(config.get('storage_url'))
    if response.status_code != 200:
        raise Exception("Response not 200")
    # Parsing XML
    dictxml = xmltodict.parse(response.text)

    # Contents
    keys = [data['Key'] for data in dictxml['ListBucketResult']['Contents'] if data['Key'].endswith('.dwg')]

    return keys

def request_data_dwg(key: str):
    response = requests.get(config.get('storage_url') + key, headers={
        'Accept': 'application/json',
    })
    if response.status_code != 200:
        raise Exception("Response not 200")
    
    return response.json()

# Retrieving keys
print('Retrieving data keys...') # Logging keys
keys = request_data_all()
print(f'Receiving {len(keys)} keys') # Logging after keys

# Prepare heads
heads = ['id', 'utbk_no', 'name', 'date_of_birth', 'bidik_misi', 'passed', 'ptn', 'ptn_code', 'prodi', 'prodi_code', 'next_url']

# Preparing files
print('Initializing SQLite3 and CSV Files') # Logging files

sqlite_file = sqlite3.connect(snbt_files['db'])
sqlite_cursor = sqlite_file.cursor()

csv_file = open(snbt_files['excel'], "w+")
csv_file_writer = csv.writer(csv_file, delimiter=',', quotechar=';')

csv_file_writer.writerow(heads)

## SQLite3 create table if not exists
sqlite_create_table_query = """
    CREATE TABLE IF NOT EXISTS snbt_dump (
        id BIGINT AUTO_INCREMENT PRIMARY KEY,
        utbk_no VARCHAR(255) NOT NULL,
        name VARCHAR(255) NOT NULL,
        date_of_birth VARCHAR(50) NOT NULL,
        bidik_misi INT(1),
        passed INT(1) NOT NULL,
        ptn VARCHAR(255),
        ptn_code INT,
        prodi VARCHAR(255),
        prodi_code INT,
        next_url VARCHAR(255)
    )
"""

sqlite_insert_row_query = """
    INSERT INTO snbt_dump (
        id,
        utbk_no,
        name,
        date_of_birth,
        bidik_misi,
        passed,
        ptn,
        ptn_code,
        prodi,
        prodi_code,
        next_url
    ) VALUES (
        ?,
        ?,
        ?,
        ?,
        ?,
        ?,
        ?,
        ?,
        ?,
        ?,
        ?
    )
"""

sqlite_cursor.execute(sqlite_create_table_query)
print('Files successfuly initialized') # Logging files

# Fetching and write
index = 1
for key in keys:
    print(f'Fetching {key}')
    data = request_data_dwg(key)

    rows = [
        index,
        data['no'],
        data['na'],
        data['dob'],
        data['bm'],
        data['ac'],
        data['npt'],
        data['kpt'],
        data['nps'],
        data['kps'],
        data['upt'],
    ]

    csv_file_writer.writerow(rows)
    sqlite_cursor.execute(sqlite_insert_row_query, rows)

    index += 1


# Closing all state
csv_file.close()
sqlite_cursor.close()
```

Setelah kita run, boom kita dapat data sebanyak dibawah ini
![image](https://github.com/user-attachments/assets/25ad6889-e8ef-4cff-881e-41308fa3dc54)

## Penutup
Mungkin saya bakalan update lagi mengenai SNBT Dumper ini di next article. Thanks for reading guys!
