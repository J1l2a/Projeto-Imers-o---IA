import google.generativeai as genai
import os

try:
    genai.configure(api_key=os.environ["GOOGLE_API_KEY"])
except KeyError:
    print("ERRO: A variável de ambiente 'GOOGLE_API_KEY' não está configurada.")
    exit()

MODELO_CHATBOT = "gemini-1.5-flash-latest"

SYSTEM_INSTRUCTIONS_MAHA = """
Você é Maha, uma consultora virtual amigável e prestativa da loja de motos Yamaha MotoCenter.
Seu objetivo principal é ajudar clientes que demonstraram interesse em nossas motos.
Você deve fornecer informações sobre modelos Yamaha, tirar dúvidas iniciais, e direcionar
para a equipe de vendas ou outras áreas da loja quando necessário.

Mantenha um tom de conversa amigável, mas profissional, adequado para WhatsApp.
Seja concisa e direta ao responder.
Não invente informações sobre preços exatos sem confirmar ou ofereça promoções que não foram instruídas.
Se a pergunta for complexa ou exigir consulta (ex: financiamento detalhado, estoque exato), indique que vai verificar ou direcionar para a equipe humana.
Responda apenas sobre assuntos relacionados a motos Yamaha e à loja Yamaha MotoCenter.

Informações da loja (para referência, use quando apropriado):
- Nome da loja: Yamaha MotoCenter
- Endereço: Rua das Motos, 123, Bairro Motor, Cidade - SP (CEP: 01000-000)
- Telefone (para contato humano, se necessário): (11) 5555-1234
- Horário de Funcionamento: Segunda a Sexta, das 9h às 18h. Sábados, das 9h às 13h. Fechado Domingos e Feriados.
"""

try:
    model = genai.GenerativeModel(
        model_name=MODELO_CHATBOT,
        system_instruction=SYSTEM_INSTRUCTIONS_MAHA
    )

    print("\n--- Simulador de Conversa WhatsApp Yamaha ---")
    print("*(Simulando a conversa com a Consultora Maha. Digite 'fim' para encerrar)*")
    print("---------------------------------------------\n")

    nome_cliente = input("Cliente: (Digite seu nome ou pressione Enter para usar 'Cliente Yamaha') ")
    if not nome_cliente.strip():
        nome_cliente = "Cliente Yamaha"

    print(f"Maha: Olá {nome_cliente}! Seja bem-vindo(a) à Yamaha MotoCenter! Em que posso te ajudar hoje?")
    print("-" * 20)

    initial_history = [
        {'role': 'user', 'parts': [f"Meu nome é {nome_cliente}. Quero informações sobre motos Yamaha."]},
        {'role': 'model', 'parts': [f"Olá {nome_cliente}! Seja bem-vindo(a) à Yamaha MotoCenter! Em que posso te ajudar hoje?"]}
    ]
    chat = model.start_chat(history=initial_history)

    prompt = input(f"{nome_cliente}: ")

    while prompt.lower() != "fim":
        try:
            resposta = chat.send_message(prompt)
            print(f"Maha: {resposta.text}")
            print("-" * 20)
        except Exception as e:
            print(f"Ocorreu um problema na conversa: {e}")
        prompt = input(f"{nome_cliente}: ")

    print("\n---------------------------------------------")
    print("Conversa encerrada. A Yamaha MotoCenter agradece seu interesse!")
    print("Se desejar atendimento humano, ligue para (11) 5555-1234 ou visite nossa loja.")
    print("---------------------------------------------")

except Exception as e:
    print(f"\nErro fatal ao iniciar o simulador: {e}")# Projeto-Imers-o---IA
    
