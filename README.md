
local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local playerBackpack = player:WaitForChild("Backpack")
local user = game.Players.LocalPlayer
local Character = user.Character or user.CharacterAdded:Wait()

-- Criar a interface
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local TitleLabel = Instance.new("TextLabel")
local AutoFarmButton = Instance.new("TextButton")
local StatusLabel = Instance.new("TextLabel")
local MessageLabel = Instance.new("TextLabel")

local farming = false

-- Configurações da interface
ScreenGui.Parent = playerGui
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
MainFrame.Position = UDim2.new(0.1, 0, 0.1, 0)
MainFrame.Size = UDim2.new(0, 400, 0, 200)
MainFrame.BorderSizePixel = 0

TitleLabel.Parent = MainFrame
TitleLabel.BackgroundTransparency = 1
TitleLabel.Size = UDim2.new(0, 400, 0, 30)
TitleLabel.Position = UDim2.new(0, 0, 0, 10)
TitleLabel.Text = "Auto Farm Level - Blox Fruits"
TitleLabel.TextColor3 = Color3.fromRGB(255, 215, 0)
TitleLabel.TextSize = 22
TitleLabel.Font = Enum.Font.SourceSansBold

AutoFarmButton.Parent = MainFrame
AutoFarmButton.BackgroundColor3 = Color3.fromRGB(70, 130, 180)
AutoFarmButton.Size = UDim2.new(0, 380, 0, 40)
AutoFarmButton.Position = UDim2.new(0, 10, 0, 50)
AutoFarmButton.Text = "Iniciar Auto Farm"
AutoFarmButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoFarmButton.TextSize = 16
AutoFarmButton.Font = Enum.Font.SourceSansBold

StatusLabel.Parent = MainFrame
StatusLabel.BackgroundTransparency = 1
StatusLabel.Size = UDim2.new(0, 380, 0, 20)
StatusLabel.Position = UDim2.new(0, 10, 0, 100)
StatusLabel.Text = "Status: Inativo"
StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
StatusLabel.TextSize = 14

MessageLabel.Parent = MainFrame
MessageLabel.BackgroundTransparency = 1
MessageLabel.Size = UDim2.new(0, 380, 0, 20)
MessageLabel.Position = UDim2.new(0, 10, 0, 130)
MessageLabel.TextColor3 = Color3.fromRGB(255, 215, 0)
MessageLabel.TextSize = 14

local function moveTo(position)
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.CFrame = CFrame.new(position)
    end
end

local function attackEnemy(enemy)
    if enemy and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
        local tool = playerBackpack:FindFirstChildOfClass("Tool")
        if tool then
            player.Character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
            wait(0.5) -- Aguarda meio segundo antes de atacar
            tool:Activate() -- Ataca o inimigo
            MessageLabel.Text = "Atacando: " .. enemy.Name
        end
    end
end

local function acceptMission(missionNPC)
    -- Lógica para aceitar a missão do NPC
    if missionNPC and missionNPC:FindFirstChild("ProximityPrompt") then
        missionNPC.ProximityPrompt:Invoke()
        MessageLabel.Text = "Missão aceita: " .. missionNPC.Name
    end
end

local function autoFarm()
    while farming do
        local missionNPC = game.Workspace:FindFirstChild("NomeDoNPC") -- Substitua "NomeDoNPC" pelo nome do NPC
        if missionNPC then
            moveTo(missionNPC.Position)
            acceptMission(missionNPC)
            
            -- Pega o nível necessário da próxima missão
            local targetLevel = 100 -- Defina o nível necessário para a próxima missão

            while farming and user.Level < targetLevel do
                for _, enemy in pairs(workspace:GetChildren()) do
                    if enemy:IsA("Model") and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                        if enemy.Name:lower() == "nome_do_inimigo" then -- Substitua pelo nome do inimigo
                            attackEnemy(enemy)
                            wait(1) -- Espera 1 segundo entre ataques
                        end
                    end
                end
                wait(2) -- Espera 2 segundos antes de verificar novamente
            end
            
            MessageLabel.Text = "Nível atingido! Pare o farm."
            farming = false
            StatusLabel.Text = "Status: Inativo"
            AutoFarmButton.Text = "Iniciar Auto Farm"
        end
        wait(1) -- Espera 1 segundo antes de verificar novamente
    end
end

AutoFarmButton.MouseButton1Click:Connect(function()
    farming = not farming
    if farming then
        StatusLabel.Text = "Status: Ativo"
        AutoFarmButton.Text = "Parar Auto Farm"
        autoFarm()
    else
        StatusLabel.Text = "Status: Inativo"
        AutoFarmButton.Text = "Iniciar Auto Farm"
    end
end)
