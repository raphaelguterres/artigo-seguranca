# SOC Lab com Wazuh: Windows Telemetry, FIM e Detection Engineering

## Resumo

Construí um laboratório SOC reproduzível com Wazuh 4.14.9, usando VirtualBox, Kali Linux como servidor e Windows Server como endpoint. O objetivo foi demonstrar o fluxo completo de monitoramento: coleta, detecção, investigação e evidência.

## Arquitetura

- **Wazuh Manager/Indexer/Dashboard:** stack Docker single-node na VM Kali.
- **Endpoint Windows Server:** agente Wazuh `WIN-TP3GFAE20CB`, ID `004`.
- **Endpoint Linux:** agente de laboratório ativo para validar regras customizadas.
- **Rede:** laboratório isolado entre as VMs, sem testes contra terceiros.

## O que foi implementado

1. Stack Wazuh em Docker, com certificados e indexação em estado `green`.
2. Regras locais para brute force, comando suspeito, possível exfiltração e elevação de privilégio.
3. Coleta de Event Channels Windows:
   - `Security`
   - `System`
   - `Application`
   - `Microsoft-Windows-PowerShell/Operational`
4. Evento nativo controlado no Windows:
   - Canal: `System`
   - Fonte: `WazuhLab`
   - ID: `501`
   - Mensagem: `WAZUH_LAB_SYSTEM`
5. Teste de FIM no Windows com criação e alteração de arquivo em diretório monitorado.
6. Alerta confirmado no Manager com `syscheck_integrity_changed` e hashes antes/depois.

## Evidências

![Alerta FIM recebido pelo agente Windows 004](https://raw.githubusercontent.com/raphaelguterres/artigo-seguranca/root/assets/wazuh/kali-alerts-agent004.png)

![Evento Windows confirmado](https://raw.githubusercontent.com/raphaelguterres/artigo-seguranca/root/assets/wazuh/windows-event-confirmed.svg)

## Resultado técnico

O laboratório comprovou que um evento gerado no endpoint Windows pode ser coletado pelo agente, processado pelo Manager e transformado em evidência investigável. No FIM, o alerta apresentou caminho, timestamp, agente e hashes MD5/SHA1/SHA256 antes/depois.

## Competências demonstradas

`Wazuh` · `SIEM` · `SOC` · `Windows Event Channels` · `PowerShell telemetry` · `FIM` · `Detection Engineering` · `Incident Investigation` · `MITRE ATT&CK`

## Próximas melhorias

- Ativar Sysmon para processos, rede e criação de processos.
- Criar matriz MITRE ATT&CK para os cenários.
- Capturar a mesma investigação no Dashboard.
- Adicionar playbooks de resposta para cada severidade.

> Todos os testes foram executados em ambiente isolado e autorizado, com eventos inofensivos e sem uso de malware ou credenciais reais.
