# SmartAcessSystem
# Descrição
Este projeto é uma solução de controle de acesso para academias, desenvolvida como uma situação de aprendizagem. Utiliza as tecnologias Tago.io, Node-RED e Arduino para criar um sistema seguro e eficiente para gerenciar a entrada dos membros.

# Funcionalidades
• Controle de Acesso: A porta da academia só abre se o membro tiver uma tag válida, com pagamento e matrícula em dia. Caso contrário, a porta permanece fechada.
• Segurança Adicional: Após a abertura da porta, um sensor infravermelho é ativado para verificar se o membro realmente passou pela porta. Se o sensor detectar a passagem, a porta se • • fecha automaticamente para garantir a segurança e evitar acesso não autorizado.

# Tecnologias Utilizadas
• Tago.io: Plataforma para gerenciamento de dados e integração com o sistema.
• Node-RED: Ferramenta para programação de fluxos e integração de dispositivos.
• Arduino: Microcontrolador responsável pelo controle físico da porta e sensores.

# Como Funciona
• Validação da Tag: O membro apresenta a tag na entrada. O sistema verifica a validade da tag e confirma se o pagamento e a matrícula estão em dia.
• Abertura da Porta: Se a validação for bem-sucedida, a porta se abre automaticamente.
• Detecção de Passagem: Um sensor infravermelho monitora a passagem do membro. Se o sensor detectar que a porta foi atravessada, a porta se fecha automaticamente para garantir que apenas o membro autorizado tenha acesso.

# Como Executar o Projeto
1º Configuração do Arduino: Conecte os sensores e a estrutura de controle da porta ao Arduino e faça o upload do código fornecido.
2º Configuração do Node-RED: Configure os fluxos para gerenciar a comunicação entre o Arduino e o Tago.io.
3º Integração com o Tago.io: Configure a plataforma Tago.io para monitorar e gerenciar os dados das tags e status de pagamento.

# Contribuições
Sinta-se à vontade para contribuir com melhorias ou correções para este projeto. Se você encontrar algum bug ou tiver sugestões, por favor, abra uma issue ou envie um pull request.

# Licença
Este projeto está licenciado sob a Licença MIT.
