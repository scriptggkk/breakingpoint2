-- Configurações iniciais
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Variáveis do exploit
local autoHeadshotEnabled = false
local menuVisible = true
local silentAimEnabled = false
local fov = 30 -- Campo de visão para silent aim

-- Criação da GUI bonita
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BPExploitMenu"
ScreenGui.Parent = game:GetService("CoreGui")

-- Frame principal do menu
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0.25, 0, 0.35, 0)
MainFrame.Position = UDim2.new(0.05, 0, 0.3, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

-- Borda decorativa
local Border = Instance.new("Frame")
Border.Size = UDim2.new(1, 0, 0.01, 0)
Border.Position = UDim2.new(0, 0, 0, 0)
Border.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
Border.BorderSizePixel = 0
Border.Parent = MainFrame

-- Título do menu
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0.1, 0)
Title.Position = UDim2.new(0, 0, 0.02, 0)
Title.BackgroundTransparency = 1
Title.Text = "Breaking Point Exploit"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.Parent = MainFrame

-- Botão de fechar (X)
local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0.1, 0, 0.1, 0)
CloseButton.Position = UDim2.new(0.9, 0, 0, 0)
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "X"
CloseButton.TextSize = 14
CloseButton.Parent = MainFrame

-- Toggle para Auto Headshot
local HeadshotToggle = Instance.new("TextButton")
HeadshotToggle.Size = UDim2.new(0.9, 0, 0.12, 0)
HeadshotToggle.Position = UDim2.new(0.05, 0, 0.15, 0)
HeadshotToggle.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
HeadshotToggle.Text = "Auto Headshot: OFF"
HeadshotToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
HeadshotToggle.Font = Enum.Font.Gotham
HeadshotToggle.TextSize = 14
HeadshotToggle.Parent = MainFrame

-- Toggle para Silent Aim
local SilentAimToggle = Instance.new("TextButton")
SilentAimToggle.Size = UDim2.new(0.9, 0, 0.12, 0)
SilentAimToggle.Position = UDim2.new(0.05, 0, 0.3, 0)
SilentAimToggle.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
SilentAimToggle.Text = "Silent Aim: OFF"
SilentAimToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
SilentAimToggle.Font = Enum.Font.Gotham
SilentAimToggle.TextSize = 14
SilentAimToggle.Parent = MainFrame

-- Controle de FOV
local FOVLabel = Instance.new("TextLabel")
FOVLabel.Size = UDim2.new(0.9, 0, 0.08, 0)
FOVLabel.Position = UDim2.new(0.05, 0, 0.45, 0)
FOVLabel.BackgroundTransparency = 1
FOVLabel.Text = "FOV: 30°"
FOVLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
FOVLabel.Font = Enum.Font.Gotham
FOVLabel.TextSize = 12
FOVLabel.TextXAlignment = Enum.TextXAlignment.Left
FOVLabel.Parent = MainFrame

local FOVSlider = Instance.new("TextButton")
FOVSlider.Size = UDim2.new(0.8, 0, 0.08, 0)
FOVSlider.Position = UDim2.new(0.1, 0, 0.55, 0)
FOVSlider.BackgroundColor3 = Color3.fromRGB(70, 70, 80)
FOVSlider.Text = "Ajustar FOV"
FOVSlider.TextColor3 = Color3.fromRGB(255, 255, 255)
FOVSlider.Font = Enum.Font.Gotham
FOVSlider.TextSize = 12
FOVSlider.Parent = MainFrame

-- Info
local InfoLabel = Instance.new("TextLabel")
InfoLabel.Size = UDim2.new(0.9, 0, 0.1, 0)
InfoLabel.Position = UDim2.new(0.05, 0, 0.8, 0)
InfoLabel.BackgroundTransparency = 1
InfoLabel.Text = "Ative o Auto Headshot para facas sempre acertarem a cabeça"
InfoLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
InfoLabel.Font = Enum.Font.Gotham
InfoLabel.TextSize = 10
InfoLabel.TextWrapped = true
InfoLabel.Parent = MainFrame

-- Função para encontrar o jogador mais próximo no FOV
local function getClosestPlayer()
    local closestPlayer = nil
    local closestDistance = fov
    local camera = workspace.CurrentCamera
    
    for _, target in pairs(Players:GetPlayers()) do
        if target ~= player and target.Character and target.Character:FindFirstChild("Head") then
            local headPos = target.Character.Head.Position
            local screenPos, onScreen = camera:WorldToScreenPoint(headPos)
            
            if onScreen then
                local mousePos = Vector2.new(mouse.X, mouse.Y)
                local targetPos = Vector2.new(screenPos.X, screenPos.Y)
                local distance = (mousePos - targetPos).Magnitude
                
                if distance < closestDistance then
                    closestDistance = distance
                    closestPlayer = target
                end
            end
        end
    end
    
    return closestPlayer
end

-- Hook para o lançamento de facas
local originalThrow
local function hookKnifeThrow()
    -- Encontrar a função de lançamento de facas no jogo
    -- (Esta parte precisa ser adaptada para a estrutura específica do Breaking Point)
    local knifeScript = player.Character:FindFirstChildWhichIsA("LocalScript") -- Ajuste isso
    
    if knifeScript and knifeScript:FindFirstChild("ThrowKnife") then
        originalThrow = knifeScript.ThrowKnife
        knifeScript.ThrowKnife = function(...)
            if autoHeadshotEnabled then
                local target = getClosestPlayer()
                if target and target.Character and target.Character:FindFirstChild("Head") then
                    -- Modificar a direção para acertar a cabeça
                    local args = {...}
                    args[1] = target.Character.Head.Position -- Ajuste a posição de lançamento
                    return originalThrow(unpack(args))
                end
            end
            return originalThrow(...)
        end
    end
end

-- Configurar eventos do menu
HeadshotToggle.MouseButton1Click:Connect(function()
    autoHeadshotEnabled = not autoHeadshotEnabled
    HeadshotToggle.Text = "Auto Headshot: " .. (autoHeadshotEnabled and "ON" or "OFF")
    HeadshotToggle.BackgroundColor3 = autoHeadshotEnabled and Color3.fromRGB(0, 150, 255) or Color3.fromRGB(60, 60, 70)
    
    if autoHeadshotEnabled then
        hookKnifeThrow()
    elseif originalThrow then
        -- Restaurar a função original
        local knifeScript = player.Character:FindFirstChildWhichIsA("LocalScript")
        if knifeScript and knifeScript:FindFirstChild("ThrowKnife") then
            knifeScript.ThrowKnife = originalThrow
        end
    end
end)

SilentAimToggle.MouseButton1Click:Connect(function()
    silentAimEnabled = not silentAimEnabled
    SilentAimToggle.Text = "Silent Aim: " .. (silentAimEnabled and "ON" or "OFF")
    SilentAimToggle.BackgroundColor3 = silentAimEnabled and Color3.fromRGB(0, 150, 255) or Color3.fromRGB(60, 60, 70)
end)

FOVSlider.MouseButton1Click:Connect(function()
    fov = fov + 5
    if fov > 60 then fov = 10 end
    FOVLabel.Text = "FOV: " .. fov .. "°"
end)

CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
    -- Restaurar a função original ao fechar
    if originalThrow then
        local knifeScript = player.Character:FindFirstChildWhichIsA("LocalScript")
        if knifeScript and knifeScript:FindFirstChild("ThrowKnife") then
            knifeScript.ThrowKnife = originalThrow
        end
    end
end)

-- Atualizar quando o personagem spawnar
player.CharacterAdded:Connect(function(character)
    wait(1) -- Esperar os scripts carregarem
    if autoHeadshotEnabled then
        hookKnifeThrow()
    end
end)

-- Desenhar FOV no screen (opcional)
if silentAimEnabled then
    local circle = Drawing.new("Circle")
    circle.Visible = true
    circle.Radius = fov
    circle.Color = Color3.fromRGB(0, 150, 255)
    circle.Thickness = 1
    circle.Position = Vector2.new(mouse.X, mouse.Y)
    
    RunService.RenderStepped:Connect(function()
        circle.Position = Vector2.new(mouse.X, mouse.Y)
        circle.Radius = fov
        circle.Visible = silentAimEnabled
    end)
end
