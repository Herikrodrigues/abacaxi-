#linguagem em payton

candidatos = {
    1: "igor dos santos",
    2: "isabelly marques",
    3: "Carlos Santos"
}

votos = {candidato: 0 for candidato in candidatos}

def exibir_candidatos():
    print("Candidatos disponíveis:")
    for numero, nome in candidatos.items():
        print(f"{numero}: {nome}")

def votar(numero_candidato):
    if numero_candidato in candidatos:
        votos[numero_candidato] += 1
        print(f"Voto registrado para {candidatos[numero_candidato]}!")
    else:
        print("Número de candidato inválido. Tente novamente.")

while True:
    exibir_candidatos()
    try:
        numero_voto = int(input("Digite o número do candidato (ou 0 para sair): "))
        if numero_voto == 0:
            break
        votar(numero_voto)
    except ValueError:
        print("Entrada inválida. Digite um número válido.")

print("\nResultados da votação:")
for numero, nome in candidatos.items():
    print(f"{nome}: {votos[numero]} votos")
