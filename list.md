# 4 ANÁLISE DA RESPOSTA TRANSITÓRIA DE SISTEMAS DE 1A ORDEM
## Exercício 1

````cpp
R = 560e3;
C = 10e-6;

s = tf('s');
H = 1 / (1 + s*R*C);

t = linspace(0, 10, 1000);

u_degrau = ones(size(t));
y_degrau = lsim(H, u_degrau, t);

u_rampa = t;
y_rampa = lsim(H, u_rampa, t);

figure;

subplot(2, 1, 1)
plot(t, y_degrau, 'b')
xlabel('T (s)')
ylabel('Saída')
title('Resposta ao Degrau')
legend('Saída (Degrau)', 'location', 'northwest')

subplot(2, 1, 2)
plot(t, u_rampa, 'r--', t, y_rampa, 'b')
xlabel('T (s)')
ylabel('Saída')
title('Resposta à Rampa')
legend({'Entrada (Rampa)', 'Saída (Rampa)'}, 'location', 'northwest')

````

## Exercício 2

````cpp
s = tf('s');
H = 3 / (s + 5);

t = linspace(0, 10, 1000);

u = 2 * ones(size(t));

y = lsim(H, u, t);

c_t = 6 * (1 - exp(-5 * t));

figure;
plot(t, y, 'b', t, c_t, 'r');
xlabel('Tempo (s)');
ylabel('Saída');
title('Comparação entre a resposta simulada e analítica');
legend({'Simulada', 'Analítica'}, 'location', 'southeast');

valor_final_teorico = 2 * 3 / 5;
disp(['Valor de estabilização teórico: ', num2str(valor_final_teorico)]);

````

## Exercício 3

````cpp
C = 1;
R = 1;
theta_f = 55;
theta_0 = 25;
theta_m = 50;
t = 3 * 60;

s = tf('s'); 
H = 1 / (C * s + 1);

tau = -t / log((theta_m - theta_f) / (theta_0 - theta_f));
disp(['Constante de tempo: ', num2str(tau)]);

H = 1 / (tau * s + 1);

tempo_simulacao = linspace(0, 300, 1000);
theta_f_simulacao = 55 * ones(size(tempo_simulacao));

theta_m_simulacao = lsim(H, theta_f_simulacao, tempo_simulacao);

theta_f_crescente = 25 + (6 / 60) * tempo_simulacao;

theta_m_crescente = lsim(H, theta_f_crescente, tempo_simulacao);

erro = theta_f_crescente - theta_m_crescente;

figure;

subplot(3, 1, 1);
plot(tempo_simulacao, theta_m_simulacao, 'b', 'LineWidth', 2);
xlabel('Tempo (s)');
ylabel('Temperatura (ºC)');
title('Resposta do Termômetro à Mudança de Temperatura');
grid on;

subplot(3, 1, 2);
plot(tempo_simulacao, theta_f_crescente, 'r', 'LineWidth', 2); hold on;
plot(tempo_simulacao, theta_m_crescente, 'b', 'LineWidth', 2);
xlabel('Tempo (s)');
ylabel('Temperatura (ºC)');
title('Temperatura da Fonte e do Termômetro');
legend({'Fonte', 'Termômetro'}, 'location', 'northwest');
grid on;

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
s = tf('s');

sistema1 = 15 / (s^2 + 10 * s + 15);
sistema2 = 25 / (s^2 + 10 * s + 25);
sistema3 = 60 / (s^2 + 10 * s + 60);

t = 0:0.001:4;

u = ones(size(t));

y1 = lsim(sistema1, u, t);
y2 = lsim(sistema2, u, t);
y3 = lsim(sistema3, u, t);

figure;
plot(t, y1, 'b', 'LineWidth', 2); hold on;
plot(t, y2, 'r', 'LineWidth', 2);
plot(t, y3, 'g', 'LineWidth', 2);
legend("S1", "S2", "S3");
xlabel("T (s)");
ylabel("Amplitude");
title("Respostas ao Degrau dos Sistemas");
grid on;

disp("Pólos dos sistemas:");

poles1 = roots([1 10 15]);
disp("Sistema 1:");
disp(poles1);

poles2 = roots([1 10 25]);
disp("Sistema 2:");
disp(poles2);

poles3 = roots([1 10 60]);
disp("Sistema 3:");
disp(poles3);

````

## Exercício 2

````
s = tf('s');

G = 10 / (s * (s + 2));

H = 1;

FTMF = G / (1 + G * H);

t = linspace(0, 10, 1000);

u = ones(size(t));

y = lsim(FTMF, u, t);

figure;
plot(t, y, 'b', 'LineWidth', 2);
xlabel("T (s)");
ylabel("Amplitude");
title("Resposta ao Degrau - Sistema em Malha Fechada");
grid on;

````

## Exercício 3

````
s = tf('s');

G1 = 4.8 / (s^2 + 2*s + 4.8);
G2 = 2.8 / (s^2 + 2*s + 2.8);
G3 = 2 / (s^2 + 2*s + 2);

poles_G1 = roots([1 2 4.8]);
poles_G2 = roots([1 2 2.8]);
poles_G3 = roots([1 2 2]);

disp("Polos do sistema G1:");
disp(poles_G1);
disp("Polos do sistema G2:");
disp(poles_G2);
disp("Polos do sistema G3:");
disp(poles_G3);

t = linspace(0, 5, 1000);

u = ones(size(t));

y1 = lsim(G1, u, t);
y2 = lsim(G2, u, t);
y3 = lsim(G3, u, t);

figure;
plot(t, y1, 'r', 'LineWidth', 2);
hold on;
plot(t, y2, 'g', 'LineWidth', 2);
plot(t, y3, 'b', 'LineWidth', 2);
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
## Exercício 1

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

## Exercício 2
````
num = 16*[1 1];
den = [1 4 16];
z_values = [Inf 12 6 4 2];
t = 0:0.01:10;
figure;
hold on;
for i = 1:length(z_values)
    z = z_values(i);
    sys = tf(num*z, [den z]);
    step(sys, t);
    [y, t] = step(sys);
    Mp = max(y) - 1;
    tp = find(y == max(y), 1);
    ts = find(y >= 0.95 & y <= 1.05, 1);
    plot(t, y);
    legend_str{i} = sprintf('z = %g', z);
end
xlabel('Tempo (s)');
ylabel('Saída');
title('Resposta ao Degrau para Diferentes Valores de z');
legend(legend_str);
grid on;

````


## Exercício 3
````

num = 16; % Numerador da função de transferência
den = [1 4 16]; % Denominador fixo

p_values = [Inf 12 6 4 2];

t = 0:0.01:10;

figure;
hold on;

for i = 1:length(p_values)
    p = p_values(i);
    sys = tf(num, [1 4 16 p]);
    [y, t] = step(sys);
    plot(t, y);
    legend_str{i} = sprintf('p = %g', p);
end

xlabel('Tempo (s)');
ylabel('Saída');
title('Resposta ao Degrau para Diferentes Valores de p');
legend(legend_str);
grid on;

````
