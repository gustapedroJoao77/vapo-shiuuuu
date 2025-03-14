-- Script simples para jogo de tiro no Roblox

-- Configurações
local FOVCircle = true
local ShowArrows = true
local Active = true -- Para ativar/desativar o script

-- Criação de círculos FOV
local function CreateFOV(radius, color)
    local fovCircle = Drawing.new("Circle")
    fovCircle.Thickness = 2
    fovCircle.Radius = radius
    fovCircle.Color = color
    fovCircle.Filled = false
    fovCircle.Transparency = 1
    return fovCircle
end

-- Função para identificar equipes
local function GetTeamColor(player)
    if player.Team == game.Players.LocalPlayer.Team then
        return Color3.fromRGB(0, 0, 255) -- Azul para equipe amiga
    else
        return Color3.fromRGB(255, 0, 0) -- Vermelho para equipe inimiga
    end
end

-- Função principal
local function UpdateMarkers()
    if not Active then
        return
    end

    for _, player in ipairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer then
            local character = player.Character
            if character and character:FindFirstChild("HumanoidRootPart") then
                local rootPart = character.HumanoidRootPart
                local screenPosition, onScreen = workspace.CurrentCamera:WorldToViewportPoint(rootPart.Position)

                -- Desenhar FOV e setas se o jogador estiver no campo de visão
                if onScreen and FOVCircle then
                    local color = GetTeamColor(player)
                    local fovIndicator = CreateFOV(100, color) -- Raio ajustável do FOV
                    fovIndicator.Position = Vector2.new(screenPosition.X, screenPosition.Y)
                elseif ShowArrows then
                    -- Implementar lógica de setas apontando para jogadores fora da tela
                    print("Jogador fora da tela, desenhando seta...")
                end
            end
        end
    end
end

-- Ativar e desativar script
game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.F and not gameProcessed then
        Active = not Active
        print("Script " .. (Active and "Ativado" or "Desativado"))
    end
end)

-- Loop principal
game:GetService("RunService").RenderStepped:Connect(UpdateMarkers)
