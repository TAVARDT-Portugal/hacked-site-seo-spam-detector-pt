<p align="center">
  <a href="https://ag.tavardt.com/pt/">
    <img src="https://ag.tavardt.com/img/tavardt-banner.png" alt="TAVARDT" width="1024" />
  </a>
</p>

# Detetor de Sites Hackeados e Spam SEO

[![Detetor Spam SEO](https://img.shields.io/badge/TAVARDT-Antispam%20SEO-5b3cf5.svg)](https://tools.tavardt.com/pt/detetor-seo-spam-site-hackeado/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

O **TAVARDT Detetor de Sites Hackeados e Spam SEO** disponibiliza análise forense para aplicações web com o objetivo de detetar cloaking dirigido a motores de pesquisa, injeções ocultas de hiperligações ilegais, iframes não autorizados e scripts ofuscados em Portugal.

***

## Arquitetura de Deteção e Matriz Forense de Vetores

O cloaking e a injeção oculta de conteúdos enganam os motores de busca, permanecendo invisíveis aos utilizadores legítimos. Este comprometimento provoca penalizações manuais na Google, desindexação do domínio e destruição da autoridade da marca.

Esta ferramenta avalia a discrepância entre User-Agents, audita blocos de CSS dissimulados e isola vetores maliciosos sem requerer dependências externas.

O motor analítico avalia a integridade do DOM, a variação volumétrica das respostas e padrões de código malicioso a partir de 100 pontos base:

<table>
  <thead>
    <tr>
      <th>Vetor de Comprometimento</th><th>Lógica de Deteção</th><th>Dedução</th><th>Impacto na Integridade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Cloaking Diferencial</strong></td><td>Compara o User-Agent do navegador com o Googlebot. Sinalizado quando o rácio de divergência em bytes for > 0,40 e o delta absoluto > 1000 bytes.</td><td>-30 pts</td><td>Risco crítico de penalização algorítmica e desindexação sumária nas pesquisas.</td>
    </tr>
    <tr>
      <td><strong>Injeção de Spam Oculto</strong></td><td>Analisa blocos com <code>display:none</code>, <code>opacity:0</code> ou margens negativas contendo termos de casino e produtos farmacêuticos.</td><td>-35 pts</td><td>Indício evidente de intrusão em base de dados ou ficheiros de template adulterados.</td>
    </tr>
    <tr>
      <td><strong>iframes Ocultos Suspeitos</strong></td><td>Identifica iframes com dimensões nulas ou estilos ocultos ausentes na lista de parceiros de segurança homologados.</td><td>-20 pts</td><td>Vetor propício a redirecionamentos não autorizados, transferências ocultas e roubo de sessões.</td>
    </tr>
    <tr>
      <td><strong>Código Ofuscado</strong></td><td>Deteta padrões repetidos de execução dinâmica como <code>eval()</code>, <code>unescape()</code> e <code>base64_decode</code> (>2 ocorrências).</td><td>-15 pts</td><td>Comportamento característico de droppers e scripts de persistência no servidor.</td>
    </tr>
    <tr>
      <td><strong>Falha de Ligação</strong></td><td>Tempo limite de resposta excedido ou erro de handshake TLS/HTTP.</td><td>-20 pts</td><td>Inacessibilidade do servidor impedindo a validação forense completa.</td>
    </tr>
  </tbody>
</table>

### Escala de Integridade e Níveis de Alerta

O utilitário atribui um índice global de integridade classificando o estado da aplicação:

* <strong>85 a 100 (CLEAN)</strong>: Nenhum indício de comprometimento detetado; integridade do DOM e consistência de resposta verificadas.
* <strong>60 a 84 (SUSPICIOUS)</strong>: Anomalias estruturais de CSS, iframes não autorizados ou divergências parciais a exigir inspeção.
* <strong>0 a 59 (COMPROMISED)</strong>: Presença confirmada de cloaking, injeção de links ou código ofuscado exigindo saneamento urgente.

***

## Instalação e Execução

Desenvolvido exclusivamente com a biblioteca padrão do Python 3.8+, sem qualquer dependência externa.

```bash
git clone https://github.com/TAVARDT-Portugal/hacked-site-seo-spam-detector-pt.git
cd hacked-site-seo-spam-detector-pt
```

### Execução Padrão via CLI

```bash
python seo_spam_detector.py exemplo.pt
```

### Saída Estruturada para Pipelines em JSON

```bash
python seo_spam_detector.py exemplo.pt -j
```

### Configuração de Timeout Personalizado

```bash
python seo_spam_detector.py exemplo.pt -j -t 15
```

***

## Alinhamento com CNCS e RGPD em Portugal

O saneamento de invasões e cloaking responde aos requisitos de segurança do **Centro Nacional de Cibersegurança (CNCS)** e às exigências do **Regulamento Geral sobre a Proteção de Dados (RGPD)**. Proteger aplicações web previne a exposição de dados de clientes.

Recomenda-se a integração de auditorias contínuas de cloaking e integridade do DOM nos processos de gestão de risco e auditoria tecnológica.

***

## Perguntas Frequentes Técnicas

### Como funciona o algoritmo de deteção de cloaking diferencial?

O motor efetua dois pedidos HTTP consecutivos ao domínio indicado, primeiro através de um User-Agent de navegador moderno e depois simulando o Googlebot oficial. Se a discrepância proporcional em bytes exceder 0,40 e a variação absoluta for superior a 1000 bytes, o sistema reporta cloaking e deduz 30 pontos.

### O que qualifica um iframe oculto como suspeito?

Qualquer elemento iframe com dimensões nulas ou regras de ocultação CSS (<code>display:none</code>) que não pertença a serviços de validação legítimos (como Cloudflare, Google reCAPTCHA, Google Tag Manager ou hCaptcha) é sinalizado como suspeito com dedução de 20 pontos.

### Quais termos de spam são identificados em blocos CSS ocultos?

A rotina inspeciona blocos ocultos através de <code>display:none</code>, <code>visibility:hidden</code>, <code>opacity:0</code> ou coordenadas negativas procurando palavras como viagra, cialis, casino, payday loan, replica watch, poker, roulette, baccarat, vape e ketamine, aplicando uma penalização de 35 pontos.

### Por que razão as chamadas a eval e unescape são penalizadas?

Aplicações web corporativas e modernas não devem recorrer a <code>eval()</code> ou <code>unescape()</code> no código cliente. Mais de duas ocorrências dessas funções sugerem a injeção de cargas dinâmicas de atacantes, com penalização de 15 pontos.

### Como consumir o resultado do diagnóstico em sistemas automatizados?

Basta invocar o utilitário com o argumento <code>-j</code> para gerar a resposta estruturada em JSON diretamente na saída padrão, facilitando a ingestão em ferramentas SIEM e pipelines de integração contínua.

***

## Desinfeção e Resposta a Incidentes

Para suporte especializado na remoção de malware, recuperação de penalizações de motores de busca e segurança aplicacional com o CNCS, contacte a **TAVARDT**.

***

## Links Úteis

* **Portal Corporativo:** https://ag.tavardt.com/pt/
* **Suíte de Diagnósticos:** https://tools.tavardt.com/pt/
* **Contacto Técnico:** https://ag.tavardt.com/pt/contacto/

***

### Licença
Distribuído sob a Licença MIT.

***

© 2026 TAVARDT. Todos os direitos reservados.
