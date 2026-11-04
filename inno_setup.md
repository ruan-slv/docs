# Guia rápido de Inno Setup

O Inno Setup é uma ferramenta gratuita para criar instaladores para aplicativos Windows. A configuração é escrita em um arquivo `.iss`, que pode ser compilado pelo aplicativo Inno Setup ou pelo compilador de linha de comando `ISCC.exe`.

## Estrutura de um instalador

Um script `.iss` é dividido em seções. As mais usadas são:

| Seção | Finalidade |
| :-- | :-- |
| `[Setup]` | Define identidade, versão, diretório e opções do instalador. |
| `[Files]` | Copia arquivos para o computador do usuário. |
| `[Icons]` | Cria atalhos no menu Iniciar ou na área de trabalho. |
| `[Tasks]` | Apresenta opções selecionáveis durante a instalação. |
| `[Run]` | Executa um programa ao final da instalação. |
| `[UninstallDelete]` | Remove arquivos ou pastas adicionais durante a desinstalação. |

## Exemplo básico

O exemplo pressupõe que o executável e os arquivos publicados estejam na pasta `publish`, ao lado do arquivo `.iss`.

```ini
#define MyAppName "Minha Aplicação"
#define MyAppVersion "1.0.0"
#define MyAppPublisher "Minha Empresa"
#define MyAppExeName "MinhaAplicacao.exe"

[Setup]
AppId=MinhaAplicacao
AppName={#MyAppName}
AppVersion={#MyAppVersion}
AppPublisher={#MyAppPublisher}
DefaultDirName={autopf}\{#MyAppName}
DefaultGroupName={#MyAppName}
PrivilegesRequired=admin
OutputDir=dist
OutputBaseFilename=MinhaAplicacao-Setup
Compression=lzma
SolidCompression=yes
WizardStyle=modern

[Files]
Source: "publish\*"; DestDir: "{app}"; Flags: ignoreversion recursesubdirs createallsubdirs

[Tasks]
Name: "desktopicon"; Description: "Criar um atalho na área de trabalho"; GroupDescription: "Atalhos:"; Flags: unchecked

[Icons]
Name: "{autoprograms}\{#MyAppName}"; Filename: "{app}\{#MyAppExeName}"
Name: "{autodesktop}\{#MyAppName}"; Filename: "{app}\{#MyAppExeName}"; Tasks: desktopicon

[Run]
Filename: "{app}\{#MyAppExeName}"; Description: "Iniciar {#MyAppName}"; Flags: postinstall nowait skipifsilent
```

`AppId` identifica o aplicativo para o instalador e para o desinstalador. Mantenha esse valor estável entre versões do mesmo produto; ao distribuir um aplicativo real, prefira definir um identificador exclusivo.

## Compilar o instalador

1. Instale o Inno Setup a partir do [site oficial](https://jrsoftware.org/isinfo.php).
2. Salve a configuração em um arquivo com extensão `.iss`.
3. Ajuste os nomes e caminhos, especialmente `MyAppExeName` e `Source`.
4. Abra o arquivo no Inno Setup e use **Compile**.
5. O instalador será gerado na pasta informada por `OutputDir` (no exemplo, `dist`).

Também é possível compilar pelo terminal:

```powershell
& "${env:ProgramFiles(x86)}\Inno Setup 6\ISCC.exe" ".\MinhaAplicacao.iss"
```

O caminho do `ISCC.exe` pode variar conforme a versão e o local de instalação.

## Diretivas e constantes úteis

- `AppVersion`: versão apresentada pelo instalador.
- `DefaultDirName`: diretório de instalação sugerido.
- `OutputDir` e `OutputBaseFilename`: pasta e nome do instalador gerado.
- `{app}`: diretório de instalação escolhido.
- `{autopf}`: diretório padrão de Program Files apropriado ao modo de instalação.
- `{autoprograms}`: pasta do menu Iniciar apropriada ao usuário ou ao computador.
- `{autodesktop}`: área de trabalho apropriada ao usuário ou ao computador.
- `PrivilegesRequired=admin`: solicita elevação para instalar com privilégios administrativos. Use `lowest` quando o aplicativo puder ser instalado sem elevação e a configuração for compatível.

## Boas práticas

- Gere os arquivos finais do aplicativo antes de compilar o instalador e confira se `Source` aponta para eles.
- Mantenha o mesmo `AppId` nas atualizações do mesmo aplicativo.
- Distribua apenas os arquivos necessários e teste a instalação e a desinstalação em uma máquina limpa.
- Não inclua senhas, tokens ou outros segredos no script ou nos arquivos empacotados.
- Consulte a [documentação oficial](https://jrsoftware.org/ishelp/) para opções avançadas, como idiomas, componentes e ações personalizadas.
