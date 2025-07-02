<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Resolver Programación Lineal</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body { font-family: Arial; margin: 20px; }
    input, button { padding: 8px; margin: 5px 0; width: 100%; }
    label { font-weight: bold; display: block; margin-top: 10px; }
    canvas { margin-top: 20px; border: 1px solid #ccc; }
  </style>
</head>
<body>
  <h2>Calculadora de Programación Lineal Gráfica</h2>

  <label for="obj">Función Objetivo (ej: 60x + 80y)</label>
  <input id="obj" placeholder="Ej: 60x + 80y">

  <label for="rest1">Restricción 1 (ej: 4x + 2y <= 100)</label>
  <input id="rest1" placeholder="Ej: 4x + 2y <= 100">

  <label for="rest2">Restricción 2 (ej: 2x + 3y <= 90)</label>
  <input id="rest2" placeholder="Ej: 2x + 3y <= 90">

  <button onclick="resolver()">Graficar</button>

  <canvas id="grafico" width="600" height="400"></canvas>

  <script>
    function parsear(expr) {
      const partes = expr.match(/([\d\.\-]*)x\s*([\+\-]\s*[\d\.\-]*)y\s*([<>]=?)\s*([\d\.\-]+)/);
      if (!partes) return null;
      let [ , A, B, op, C ] = partes;
      A = parseFloat(A || "1");
      B = parseFloat(B.replace(/\s/g, ""));
      C = parseFloat(C);
      return { A, B, op, C };
    }

    function resolver() {
      const objText = document.getElementById("obj").value;
      const r1 = parsear(document.getElementById("rest1").value);
      const r2 = parsear(document.getElementById("rest2").value);

      const objMatch = objText.match(/([\d\.\-]*)x\s*([\+\-]\s*[\d\.\-]*)y/);
      if (!r1 || !r2 || !objMatch) {
        alert("Verifica los datos ingresados");
        return;
      }

      const a = parseFloat(objMatch[1] || "1");
      const b = parseFloat(objMatch[2].replace(/\s/g, ""));

      // Resolver intersección
      const det = r1.A * r2.B - r2.A * r1.B;
      if (det === 0) {
        alert("Las restricciones no se cruzan en un punto único");
        return;
      }

      const x = (r1.C * r2.B - r2.C * r1.B) / det;
      const y = (r1.A * r2.C - r2.A * r1.C) / det;
      const Z = a * x + b * y;

      const ctx = document.getElementById("grafico").getContext("2d");
      new Chart(ctx, {
        type: 'scatter',
        data: {
          datasets: [
            {
              label: 'Punto óptimo',
              data: [{ x, y }],
              backgroundColor: 'red',
              pointRadius: 6
            }
          ]
        },
        options: {
          plugins: {
            title: {
              display: true,
              text: `Solución óptima: (${x.toFixed(2)}, ${y.toFixed(2)})  Z = ${Z.toFixed(2)}`
            }
          },
          scales: {
            x: { min: 0, max: 100 },
            y: { min: 0, max: 100 }
          }
        }
      });
    }
  </script>
</body>
</html>
