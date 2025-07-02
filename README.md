<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Programación Lineal Gráfica</title>
  <script src="https://cdn.jsdelivr.net/npm/mathjs@11.8.0/lib/browser/math.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
    }
    input, button {
      margin: 5px 0;
      width: 100%;
      padding: 8px;
    }
    canvas {
      margin-top: 20px;
      border: 1px solid #ccc;
    }
  </style>
</head>
<body>
  <h2>Calculadora de Programación Lineal (Gráfica)</h2>
  <p>Ingresa la función objetivo y dos restricciones en forma <strong>Ax + By ≤ / ≥ C</strong></p>

  <label>Función objetivo (Ej: 60x + 80y):</label>
  <input id="objetivo" placeholder="Ej: 60x + 80y">

  <label>Tipo (max o min):</label>
  <input id="tipo" placeholder="max o min">

  <label>Restricción 1:</label>
  <input id="restriccion1" placeholder="Ej: 4x + 2y <= 100">

  <label>Restricción 2:</label>
  <input id="restriccion2" placeholder="Ej: 2x + 3y <= 90">

  <button onclick="graficar()">Graficar</button>

  <canvas id="grafico" width="600" height="400"></canvas>

  <script>
    function parseRestriccion(expr) {
      const match = expr.match(/([\d\.\-]*)x\s*([\+\-]\s*[\d\.\-]*)y\s*([<>=]+)\s*([\d\.\-]+)/);
      if (!match) return null;
      let [ , A, B, op, C ] = match;
      A = parseFloat(A || "1");
      B = parseFloat(B.replace(/\s/g, ""));
      C = parseFloat(C);
      return { A, B, op, C };
    }

    function graficar() {
      const obj = document.getElementById("objetivo").value;
      const tipo = document.getElementById("tipo").value.trim().toLowerCase();
      const r1 = parseRestriccion(document.getElementById("restriccion1").value);
      const r2 = parseRestriccion(document.getElementById("restriccion2").value);

      if (!r1 || !r2 || !obj || (tipo !== "max" && tipo !== "min")) {
        alert("Completa todos los campos correctamente.");
        return;
      }

      // Calcular intersección
      const A = math.matrix([[r1.A, r1.B], [r2.A, r2.B]]);
      const b = math.matrix([r1.C, r2.C]);

      let punto;
      try {
        punto = math.lusolve(A, b);
      } catch (err) {
        alert("No se puede calcular la intersección.");
        return;
      }

      const xSol = punto[0][0];
      const ySol = punto[1][0];

      // Calcular valor objetivo
      const coefObj = obj.match(/([\d\.\-]*)x\s*([\+\-]\s*[\d\.\-]*)y/);
      const a = parseFloat(coefObj[1] || "1");
      const bObj = parseFloat(coefObj[2].replace(/\s/g, ""));
      const z = a * xSol + bObj * ySol;

      // Crear gráfica
      const ctx = document.getElementById("grafico").getContext("2d");
      const chart = new Chart(ctx, {
        type: 'scatter',
        data: {
          datasets: [
            {
              label: 'Intersección óptima',
              data: [{ x: xSol, y: ySol }],
              backgroundColor: 'red',
              pointRadius: 6
            }
          ]
        },
        options: {
          plugins: {
            title: {
              display: true,
              text: `Solución óptima: (${xSol.toFixed(2)}, ${ySol.toFixed(2)}) → Z = ${z.toFixed(2)}`
            }
          },
          scales: {
            x: {
              type: 'linear',
              position: 'bottom',
              min: 0,
              max: 100
            },
            y: {
              min: 0,
              max: 100
            }
          }
        }
      });
    }
  </script>
</body>
</html>
