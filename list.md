# 4 ANÁLISE DA RESPOSTA TRANSITÓRIA DE SISTEMAS DE 1A ORDEM
## Exercício 1

````cpp
% Parâmetros do circuito
R = 560e3; % Resistência em ohms
C = 10e-6; % Capacitância em farads

% Função de transferência no domínio de Laplace
s = tf('s');
H = 1 / (1 + s*R*C);

% Tempo de simulação
t = linspace(0, 10, 1000); % 10 segundos, 1000 pontos

% Entrada em degrau
u_degrau = ones(size(t)); % Vetor degrau (1 para todos os valores de t)
y_degrau = lsim(H, u_degrau, t); % Simulação com a entrada degrau

% Entrada em rampa
u_rampa = t; % Vetor rampa (t crescente)
y_rampa = lsim(H, u_rampa, t); % Simulação com a entrada rampa

% Plotar as respostas
figure; % Nova figura

subplot(2, 1, 1)
plot(t, y_degrau, 'b')
xlabel('Tempo (s)')
ylabel('Saída')
title('Resposta ao Degrau')
% Adiciona legenda
legend('Saída (Degrau)', 'location', 'northwest')

subplot(2, 1, 2)
plot(t, u_rampa, 'r--', t, y_rampa, 'b')
xlabel('Tempo (s)')
ylabel('Saída')
title('Resposta à Rampa')
% Adiciona legenda
legend({'Entrada (Rampa)', 'Saída (Rampa)'}, 'location', 'northwest')
````

## Exercício 2

````cpp
% Função de transferência
s = tf('s');
H = 3 / (s + 5); % Sistema contínuo com a função de transferência H(s)

% Tempo de simulação
t = linspace(0, 10, 1000); % De 0 a 10 segundos, com 1000 pontos

% Entrada em degrau de amplitude 2
u = 2 * ones(size(t)); % Vetor degrau com amplitude 2

% Resposta ao degrau (simulação)
y = lsim(H, u, t); % Simulação da saída com a entrada degrau

% Resposta analítica (cálculo manual)
c_t = 6 * (1 - exp(-5 * t)); % Fórmula analítica da resposta

% Plotar as respostas
figure; % Nova figura
plot(t, y, 'b', t, c_t, 'r'); % Resposta simulada e analítica
xlabel('Tempo (s)');
ylabel('Saída');
title('Comparação entre a resposta simulada e analítica');
legend({'Simulada', 'Analítica'}, 'location', 'southeast');

% Cálculo do valor de estabilização manual
valor_final_teorico = 2 * 3 / 5; % Amplitude * Ganho estático
disp(['Valor de estabilização teórico: ', num2str(valor_final_teorico)]);
````

## Exercício 3

````cpp
% Parâmetros do sistema
C = 1; % Capacitância térmica
R = 1; % Resistência térmica
theta_f = 55; % Temperatura do recipiente (ºC)
theta_0 = 25; % Temperatura inicial do termômetro (ºC)
theta_m = 50; % Temperatura final após 3 minutos (ºC)
t = 3 * 60; % Tempo em segundos (3 minutos)

% a) Dedução da equação diferencial
% A equação diferencial do sistema é: C * dθ_M/dt + θ_M = θ_F

% b) Função de transferência
s = tf('s'); 
H = 1 / (C * s + 1); % Função de transferência

% c) Determinação da constante de tempo (τ)
tau = -t / log((theta_m - theta_f) / (theta_0 - theta_f));
disp(['Constante de tempo: ', num2str(tau)]);

% d) Modelo do termômetro no Matlab
% Função de transferência com a constante de tempo definida
H = 1 / (tau * s + 1); % Função de transferência do sistema

% Tempo de simulação (de 0 a 5 minutos, com 1000 pontos)
tempo_simulacao = linspace(0, 300, 1000); % Tempo de 5 minutos
theta_f_simulacao = 55 * ones(size(tempo_simulacao)); % Degrau com temperatura 55ºC

% Resposta do termômetro à temperatura da fonte
theta_m_simulacao = lsim(H, theta_f_simulacao, tempo_simulacao);

% e) Temperatura crescente e erro
% A temperatura da água cresce linearmente a 6ºC/min
theta_f_crescente = 25 + (6 / 60) * tempo_simulacao; % Crescimento linear

% Resposta do termômetro para a temperatura crescente
theta_m_crescente = lsim(H, theta_f_crescente, tempo_simulacao);

% Cálculo do erro entre a temperatura da fonte e a resposta do termômetro
erro = theta_f_crescente - theta_m_crescente;

% Gráfico dos resultados
figure;

% Subplot 1: Resposta do termômetro à mudança de temperatura
subplot(3, 1, 1);
plot(tempo_simulacao, theta_m_simulacao, 'b', 'LineWidth', 2);
xlabel('Tempo (s)');
ylabel('Temperatura (ºC)');
title('Resposta do Termômetro à Mudança de Temperatura');
grid on;

% Subplot 2: Comparação entre a temperatura da fonte e do termômetro
subplot(3, 1, 2);
plot(tempo_simulacao, theta_f_crescente, 'r', 'LineWidth', 2); hold on;
plot(tempo_simulacao, theta_m_crescente, 'b', 'LineWidth', 2);
xlabel('Tempo (s)');
ylabel('Temperatura (ºC)');
title('Temperatura da Fonte e do Termômetro');
legend({'Fonte', 'Termômetro'}, 'location', 'northwest');
grid on;

% Subplot 3: Erro entre a temperatura da fonte e a resposta do termômetro
subplot(3, 1, 3);
plot(tempo_simulacao, erro, 'k', 'LineWidth', 2);
xlabel('Tempo (s)');
ylabel('Erro (ºC)');
title('Erro entre o Termômetro e a Temperatura da Fonte');
grid on;
````
----------------------------------------------------------------------------
# 5 ANÁLISE DA RESPOSTA TRANSITÓRIA DE SISTEMAS DE 2A ORDEM
## Exercício 1

````
% Definir os sistemas no formato simbólico
s = tf('s'); % Variável 's' no domínio de Laplace

% Sistemas 1, 2 e 3
sistema1 = 15 / (s^2 + 10 * s + 15); % Sistema 1
sistema2 = 25 / (s^2 + 10 * s + 25); % Sistema 2
sistema3 = 60 / (s^2 + 10 * s + 60); % Sistema 3

% Definir o vetor de tempo
t = 0:0.001:4; % De 0 a 4 segundos, com passo 0.001

% Entrada em degrau unitário
u = ones(size(t)); % Degrau unitário

% Simular a resposta ao degrau para cada sistema
y1 = lsim(sistema1, u, t); % Resposta do Sistema 1
y2 = lsim(sistema2, u, t); % Resposta do Sistema 2
y3 = lsim(sistema3, u, t); % Resposta do Sistema 3

% Traçar os gráficos das respostas
figure; % Nova figura
plot(t, y1, 'b', 'LineWidth', 2); hold on;  % Resposta do Sistema 1
plot(t, y2, 'r', 'LineWidth', 2);  % Resposta do Sistema 2
plot(t, y3, 'g', 'LineWidth', 2);  % Resposta do Sistema 3
legend("Sistema 1", "Sistema 2", "Sistema 3");
xlabel("Tempo (s)");
ylabel("Amplitude");
title("Respostas ao Degrau dos Sistemas");
grid on;

% Determinar os pólos de cada sistema
disp("Pólos dos sistemas:");

% Sistema 1: 15 / (s^2 + 10s + 15)
poles1 = roots([1 10 15]);
disp("Sistema 1:");
disp(poles1);

% Sistema 2: 25 / (s^2 + 10s + 25)
poles2 = roots([1 10 25]);
disp("Sistema 2:");
disp(poles2);

% Sistema 3: 60 / (s^2 + 10s + 60)
poles3 = roots([1 10 60]);
disp("Sistema 3:");
disp(poles3);
````

## Exercício 2

````
% Definição da variável simbólica 's'
s = tf('s'); % Definir s como a variável de Laplace

% Função de transferência do caminho direto (G(s))
G = 10 / (s * (s + 2)); 

% Função de transferência do caminho de realimentação (H(s))
H = 1;

% Cálculo da função de transferência em malha fechada
FTMF = G / (1 + G * H);

% Tempo de simulação
t = linspace(0, 10, 1000); % De 0 a 10 segundos, com 1000 pontos

% Entrada degrau unitário
u = ones(size(t)); % Degrau unitário (1 para todos os valores de t)

% Resposta ao degrau do sistema em malha fechada
y = lsim(FTMF, u, t); % Simulação da saída com a entrada degrau

% Gráfico da resposta ao degrau
figure;
plot(t, y, 'b', 'LineWidth', 2);
xlabel("Tempo (s)");
ylabel("Amplitude");
title("Resposta ao Degrau - Sistema em Malha Fechada");
grid on;
````

## Exercício 3

````
% Definição do operador 's' no MATLAB
s = tf('s');  % Definir s como a variável de Laplace

% Definição das funções de transferência dos sistemas
G1 = 4.8 / (s^2 + 2*s + 4.8);
G2 = 2.8 / (s^2 + 2*s + 2.8);
G3 = 2 / (s^2 + 2*s + 2);

% Polos de cada sistema
poles_G1 = roots([1 2 4.8]);
poles_G2 = roots([1 2 2.8]);
poles_G3 = roots([1 2 2]);

% Exibição dos polos
disp("Polos do sistema G1:");
disp(poles_G1);
disp("Polos do sistema G2:");
disp(poles_G2);
disp("Polos do sistema G3:");
disp(poles_G3);

% Tempo de simulação
t = linspace(0, 5, 1000);  % De 0 a 5 segundos

% Entrada degrau unitário
u = ones(size(t));  % Degrau unitário

% Simulação da resposta ao degrau
y1 = lsim(G1, u, t);  % Resposta do sistema G1
y2 = lsim(G2, u, t);  % Resposta do sistema G2
y3 = lsim(G3, u, t);  % Resposta do sistema G3

% Plotagem das respostas
figure;
plot(t, y1, 'r', 'LineWidth', 2);  % Resposta do G1 (vermelho)
hold on;
plot(t, y2, 'g', 'LineWidth', 2);  % Resposta do G2 (verde)
plot(t, y3, 'b', 'LineWidth', 2);  % Resposta do G3 (azul)
xlabel("Tempo (s)");
ylabel("Amplitude");
title("Respostas ao Degrau Unitário dos Sistemas em malha aberta");
legend("Sistema G1", "Sistema G2", "Sistema G3", "Location", "southeast");
grid on;

````

## Exercício 4

````
s = tf('s');

G1 = 4.8 / (s^2 + 2*s + 4.8);
G2 = 19.2 / (s^2 + 4*s + 19.2);
G3 = 173 / (s^2 + 12*s + 173);

poles_G1 = roots([1 2 4.8]);
poles_G2 = roots([1 4 19.2]);
poles_G3 = roots([1 12 173]);

disp("Polos do sistema G1:");
disp(poles_G1);
disp("Polos do sistema G2:");
disp(poles_G2);
disp("Polos do sistema G3:");
disp(poles_G3);

t = 0:0.01:4;
u = ones(size(t));

y1 = lsim(G1, u, t);
y2 = lsim(G2, u, t);
y3 = lsim(G3, u, t);

figure;

subplot(1, 2, 1);
plot(real(poles_G1), imag(poles_G1), 'o', 'MarkerSize', 8, 'LineWidth', 2);
hold on;
plot(real(poles_G2), imag(poles_G2), 'x', 'MarkerSize', 8, 'LineWidth', 2);
plot(real(poles_G3), imag(poles_G3), '+', 'MarkerSize', 8, 'LineWidth', 2);
legend('G1', 'G2', 'G3');
title('Polos dos Sistemas');
grid on;

subplot(1, 2, 2);
plot(t, y1, 'b', 'LineWidth', 2);
hold on;
plot(t, y2, 'r', 'LineWidth', 2);
plot(t, y3, 'g', 'LineWidth', 2);
xlabel('Tempo (s)');
legend('G1', 'G2', 'G3');
title('Respostas ao Degrau dos Sistemas');
grid on;

````

## Exercício 5

````
s = tf('s');

G1 = 9 / (s^2 + 9);
G2 = 100.25 / (s^2 - s + 100.25);
G3 = 2 / (s^2 + s - 2);

poles_G1 = roots([1 0 9]);
poles_G2 = roots([1 -1 100.25]);
poles_G3 = roots([1 1 -2]);

disp("Polos de G1:");
disp(poles_G1);
disp("Polos de G2:");
disp(poles_G2);
disp("Polos de G3:");
disp(poles_G3);

t = linspace(0, 5, 1000);

u = ones(size(t));

y_G1 = lsim(G1, u, t);
y_G2 = lsim(G2, u, t);
y_G3 = lsim(G3, u, t);

figure;
plot(t, y_G1, 'r', 'LineWidth', 2);
hold on;
plot(t, y_G2, 'g', 'LineWidth', 2);
plot(t, y_G3, 'b', 'LineWidth', 2);
plot(real(poles_G1), imag(poles_G1), 'ro', 'MarkerSize', 8, 'LineWidth', 2);
plot(real(poles_G2), imag(poles_G2), 'go', 'MarkerSize', 8, 'LineWidth', 2);
plot(real(poles_G3), imag(poles_G3), 'bo', 'MarkerSize', 8, 'LineWidth', 2);

grid on;
title('Respostas ao Degrau e Polos dos Sistemas');
xlabel('Tempo (s) / Parte Real');
ylabel('Amplitude / Parte Imaginária');
legend('G1', 'G2', 'G3', ...
    'G1', 'G2', 'G3', 'Location', 'best');


````

----------------------------------------------------------------------------
# 6 ANÁLISE DA RESPOSTA TRANSITÓRIA DE SISTEMAS DE 2A ORDEM E SUPERIOR


````
wn = 3.5;
zeta = 0.48;
K = 2;

num = K * wn^2;
den = [1, 2 * zeta * wn, wn^2];
G = tf(num, den);

t = 0:0.01:5;

u1 = 2;
u2 = 3;

[y1, t1] = step(u1 * G, t);
[y2, t2] = step(u2 * G, t);

figure;
plot(t1, y1, 'b-', t2, y2, 'r--', 'LineWidth', 1.5);
legend("Degrau de Amplitude 2", "Degrau de Amplitude 3");
xlabel("Tempo (s)");
ylabel("Amplitude");
title("Resposta do Sistema");
grid on;
````


