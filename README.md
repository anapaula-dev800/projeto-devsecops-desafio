# Desafio DevSecOps — Gerenciador de Tarefas

## Sobre o Projeto
Este repositório faz parte do desafio prático do módulo de DevSecOps da ADA Tech.
Você receberá este projeto com vulnerabilidades propositais e uma pipeline incompleta.
Seu objetivo é **implementar a pipeline de segurança** e **corrigir as vulnerabilidades**.

## Estado atual
A pipeline está **incompleta**. Os steps de segurança precisam ser implementados por você.

## Sua missão
1. Implementar os steps de segurança no `pipeline.yml`
2. Fazer a pipeline **quebrar** ao detectar os problemas
3. Corrigir as vulnerabilidades encontradas
4. Fazer a pipeline **passar** com tudo verde ✅
5. Documentar o funcionamento da pipeline neste README

## O que implementar
- [ ] Secrets Scanning com **Gitleaks**
- [ ] SAST com **Semgrep**
- [ ] SCA com **Grype**
- [ ] Deploy com **GitHub Pages**

## Como a pipeline funciona

A pipeline automatiza **build → verificações de segurança → deploy**. Cada etapa de segurança é executada no CI e projetada para **falhar o job** quando encontra problemas, impedindo que código inseguro chegue à produção.

#### Secrets Scanning (Gitleaks)
**O que faz:** varre o repositório e o histórico em busca de segredos (tokens, chaves, credenciais).  
**Como roda:** action `gitleaks/gitleaks-action@v2`.  
**Por que é importante:** evita exposição acidental de credenciais; bloqueia commits que contenham segredos e força rotação/remoção antes do deploy.  
**Artefato:** SARIF ou relatório (quando configurado) para auditoria.

#### SAST (Semgrep)
**O que faz:** analisa o código-fonte em busca de padrões inseguros (injeção, validação insuficiente, uso inseguro de APIs).  
**Como roda:** Semgrep CLI (`semgrep scan --config auto --error src/ --json --output semgrep-report.json`).  
**Por que é importante:** detecta vulnerabilidades de lógica e práticas inseguras no código antes do deploy.  
**Artefato:** `semgrep-report.json` (enviado como artefato mesmo em caso de falha).

#### SCA (Grype)
**O que faz:** analisa dependências e imagens para identificar vulnerabilidades conhecidas (CVE).  
**Como roda:** Grype (`grype dir:. -o json > grype-report.json` e `grype dir:. --fail-on medium`).  
**Por que é importante:** evita publicar builds que dependam de bibliotecas vulneráveis; prioriza atualizações e mitigação.  
**Artefato:** `grype-report.json` (enviado como artefato mesmo em caso de falha).

#### Deploy (GitHub Pages)
**O que faz:** publica os arquivos estáticos (ex.: `./src`) no GitHub Pages.  
**Como roda:** `actions/configure-pages@v5`, `actions/upload-pages-artifact@v3`, `actions/deploy-pages@v5`.  
**Por que é importante:** garante que o deploy só ocorra após passar por todos os scanners de segurança, reduzindo risco de exposição.

---

### Checklist de verificação
- **Gitleaks:** step presente e falha em caso de leaks; artefato SARIF disponível.  
- **Semgrep:** roda via CLI com `--error`; gera `semgrep-report.json` e faz o job falhar em violações.  
- **Grype:** gera `grype-report.json` e usa `--fail-on medium` para bloquear builds com vulnerabilidades médias/altas.  
- **Deploy:** só é executado se todos os steps anteriores passarem; `page_url` é retornado pelo step de deploy.

---

## URL de Produção
> https://anapaula-dev800.github.io/projeto-devsecops-desafio/
