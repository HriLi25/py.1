# Phyton for Everybody Act1
python_skill_training
### Importar banco de dados do exercício ###
open r'C:\Users\Wesley\Documents\mbox.txt')

### Importar pacote para linguagem SQL dentro da Linguaguem Python ###
import sqlite3

conn = sqlite3.connect('emaildb.sqlite') ### Conecta ao Banco de Dados ###
cur = conn.cursor() ### Cria um cursor ###

cur.execute('DROP TABLE IF EXISTS Counts') ### Apaga, caso exista, a tabela 'Count' ###

cur.execute('''
CREATE TABLE Counts (email TEXT, count INTEGER)''') ### Cria a tabela 'Counts' ###

### Abrir o arquivo mbox ###
fname = input('Enter file name: ')
if (len(fname) < 1): fname = 'mbox-short.txt'
fh = open(fname)

### Processa linhas do arquivo ###
for line in fh:
    if not line.startswith('From: '): continue
    pieces = line.split()
    email = pieces[1]
    cur.execute('SELECT count FROM Counts WHERE email = ? ', (email,)) ### Atualiza os emails do Banco de Dados ###
    row = cur.fetchone()
    if row is None:
        cur.execute('''INSERT INTO Counts (email, count)
                VALUES (?, 1)''', (email,))
    else:
        cur.execute('UPDATE Counts SET count = count + 1 WHERE email = ?',
                    (email,))
    conn.commit()

### Resgata os 10 emails mais populares ###
# https://www.sqlite.org/lang_select.html
sqlstr = 'SELECT email, count FROM Counts ORDER BY count DESC LIMIT 10'

for row in cur.execute(sqlstr):
    print(str(row[0]), row[1])

cur.close() ### cessa a cenexão com o banco de dados dos emails ###
