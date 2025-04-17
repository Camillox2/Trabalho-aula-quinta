# Trabalho-aula-quinta

from flask import Flask, jsonify
import random

app = Flask(__name__)

weather_data = {
    "saopaulo": {"city": "São Paulo", "temp": random.randint(10, 35), "unit": "Celsius"},
    "riodejaneiro": {"city": "Rio de Janeiro", "temp": random.randint(20, 40), "unit": "Celsius"},
    "curitiba": {"city": "Curitiba", "temp": random.randint(5, 25), "unit": "Celsius"},
    "salvador": {"city": "Salvador", "temp": random.randint(25, 38), "unit": "Celsius"},
}

@app.route('/weather/<city>', methods=['GET'])
def get_weather(city):
    city_lower = city.lower().replace(" ", "")
    data = weather_data.get(city_lower)
    if data:
        data["temp"] = random.randint(5, 40)
        return jsonify(data)
    else:
        return jsonify({"error": "Cidade não encontrada"}), 404

if __name__ == '__main__':
    app.run(port=5001, debug=True)

from flask import Flask, jsonify
import requests

app = Flask(__name__)

API_B_URL = "http://127.0.0.1:5001/weather"

@app.route('/recommendation/<city>', methods=['GET'])
def get_recommendation(city):
    try:
        weather_response = requests.get(f"{API_B_URL}/{city}")

-------------------------------

from flask import Flask, jsonify
import requests

app = Flask(__name__)

API_B_URL = "http://127.0.0.1:5001/weather"

@app.route('/recommendation/<city>', methods=['GET'])
def get_recommendation(city):
    try:
        weather_response = requests.get(f"{API_B_URL}/{city}")

        if weather_response.status_code == 200:
            weather_data = weather_response.json()
            temp = weather_data.get("temp")

            if temp is None:
                 return jsonify({"error": "Dados de temperatura inválidos recebidos da API de clima"}), 500

            recommendation = ""
            if temp > 30:
                recommendation = "Temperatura alta! Hidrate-se bem e use protetor solar."
            elif 15 <= temp <= 30:
                recommendation = "Clima agradável!"
            else:
                recommendation = "Está frio! Considere usar um casaco."

            result = {
                "city": weather_data.get("city", city),
                "temp": temp,
                "unit": weather_data.get("unit", "Celsius"),
                "recommendation": recommendation
            }
            return jsonify(result)

        elif weather_response.status_code == 404:
            return jsonify({"error": f"Cidade '{city}' não encontrada no serviço de clima."}), 404
        else:
            return jsonify({"error": "Erro ao buscar dados do serviço de clima."}), weather_response.status_code

    except requests.exceptions.ConnectionError:
        return jsonify({"error": "Não foi possível conectar ao serviço de clima (API B). Verifique se ele está rodando."}), 503
    except Exception as e:
        return jsonify({"error": f"Ocorreu um erro inesperado: {str(e)}"}), 500

if __name__ == '__main__':
    app.run(port=5000, debug=True)


    --------------------
    pip install Flask requests
python api_b.py
python api_a.py
curl http://127.0.0.1:5000/recommendation/SaoPaulo
curl http://127.0.0.1:5000/recommendation/Curitiba
curl http://127.0.0.1:5000/recommendation/RioDeJaneiro
curl http://127.0.0.1:5000/recommendation/Salvador
curl http://127.0.0.1:5000/recommendation/Paris
