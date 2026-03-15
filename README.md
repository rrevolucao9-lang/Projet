local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "Player Hub | Rayfield",
   LoadingTitle = "Carregando Interface...",
   LoadingSubtitle = "por Rev.dev",
   ConfigurationSaving = {
      Enabled = true,
      FolderName = "RevScripts",
      FileName = "PlayerHub"
   }
})

-- Variáveis de controle
local selectedPlayer = nil
local camera = workspace.CurrentCamera

-- Aba Principal
local MainTab = Window:CreateTab("Jogadores", 4483362458) -- Ícone de usuários

local Section = MainTab:CreateSection("Controle de Players")

-- Dropdown que se auto-atualiza
local PlayerDropdown = MainTab:CreateDropdown({
   Name = "Selecionar Player",
   Options = {}, -- Começa vazio
   CurrentOption = {"Nenhum"},
   MultipleOptions = false,
   Callback = function(Option)
      selectedPlayer = game.Players:FindFirstChild(Option[1])
   end,
})

-- Função para atualizar a lista de players
local function UpdatePlayerList()
    local pList = {}
    for _, v in pairs(game.Players:GetPlayers()) do
        if v ~= game.Players.LocalPlayer then
            table.insert(pList, v.Name)
        end
    end
    PlayerDropdown:Refresh(pList, true)
end

-- Eventos para atualizar quando alguém entra ou sai
game.Players.PlayerAdded:Connect(UpdatePlayerList)
game.Players.PlayerRemoving:Connect(UpdatePlayerList)
UpdatePlayerList() -- Chamada inicial

--- Botões de Ação ---

MainTab:CreateButton({
   Name = "Teleportar até o Player",
   Callback = function()
      if selectedPlayer and selectedPlayer.Character and selectedPlayer.Character:FindFirstChild("HumanoidRootPart") then
          game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = selectedPlayer.Character.HumanoidRootPart.CFrame
          Rayfield:Notify({Title = "Sucesso", Content = "Teleportado para " .. selectedPlayer.Name, Duration = 3})
      else
          Rayfield:Notify({Title = "Erro", Content = "Selecione um player válido!", Duration = 3})
      end
   end,
})

MainTab:CreateButton({
   Name = "View Player (Espiar)",
   Callback = function()
      if selectedPlayer and selectedPlayer.Character then
          camera.CameraSubject = selectedPlayer.Character:FindFirstChild("Humanoid")
          Rayfield:Notify({Title = "View Ativo", Content = "Observando " .. selectedPlayer.Name, Duration = 3})
      end
   end,
})

MainTab:CreateButton({
   Name = "Unview (Voltar Câmera)",
   Callback = function()
      camera.CameraSubject = game.Players.LocalPlayer.Character:FindFirstChild("Humanoid")
      Rayfield:Notify({Title = "Câmera Resetada", Content = "Você voltou para seu personagem.", Duration = 3})
   end,
})

MainTab:CreateButton({
   Name = "Atualizar Lista Manualmente",
   Callback = function()
      UpdatePlayerList()
   end,
})
