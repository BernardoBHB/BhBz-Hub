-- BHBz Hub - Advanced Roblox Menu
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES INICIAIS
local Settings = {
    Aimbot = false,
    ESP = false,
    AimPart = "Head",
    FOV = 100,
    TeamCheck = true
}

-- CRIANDO A INTERFACE (MENU)
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "BHB_Menu"

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 250, 0, 350)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -175)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 2
MainFrame.Active = true
MainFrame.Draggable = true -- Para você arrastar o menu pela tela

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "BHB PLATAFORM v1"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18

-- FUNÇÃO PARA CRIAR BOTÕES (TOGGLES)
local function CreateButton(name, pos, callback)
    local Btn = Instance.new("TextButton", MainFrame)
    Btn.Size = UDim2.new(0.8, 0, 0, 35)
    Btn.Position = pos
    Btn.Text = name .. ": OFF"
    Btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    Btn.Font = Enum.Font.Gotham
    
    local enabled = false
    Btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        Btn.Text = name .. ": " .. (enabled and "ON" or "OFF")
        Btn.BackgroundColor3 = enabled and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(60, 60, 60)
        callback(enabled)
    end)
end

-- FUNÇÃO PARA O FOV CIRCLE (VISUAL)
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 1
FOVCircle.Color = Color3.fromRGB(255, 255, 255)
FOVCircle.Filled = false
FOVCircle.Transparency = 0.5

-- ELEMENTOS DO MENU
CreateButton("Aimbot", UDim2.new(0.1, 0, 0, 60), function(v) Settings.Aimbot = v end)
CreateButton("ESP", UDim2.new(0.1, 0, 0, 105), function(v) Settings.ESP = v end)

-- SELETOR DE PARTE DO CORPO (Cabeça / Barriga)
local PartBtn = Instance.new("TextButton", MainFrame)
PartBtn.Size = UDim2.new(0.8, 0, 0, 35)
PartBtn.Position = UDim2.new(0.1, 0, 0, 150)
PartBtn.Text = "Alvo: Cabeça"
PartBtn.BackgroundColor3 = Color3.fromRGB(100, 50, 150)
PartBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

PartBtn.MouseButton1Click:Connect(function()
    if Settings.AimPart == "Head" then
        Settings.AimPart = "HumanoidRootPart"
        PartBtn.Text = "Alvo: Barriga"
    else
        Settings.AimPart = "Head"
        PartBtn.Text = "Alvo: Cabeça"
    end
end)

-- AJUSTE DE FOV (Simples)
local FovLabel = Instance.new("TextLabel", MainFrame)
FovLabel.Size = UDim2.new(0.8, 0, 0, 20)
FovLabel.Position = UDim2.new(0.1, 0, 0, 200)
FovLabel.Text = "FOV: " .. Settings.FOV
FovLabel.BackgroundTransparency = 1
FovLabel.TextColor3 = Color3.fromRGB(200, 200, 200)

local FovAdd = Instance.new("TextButton", MainFrame)
FovAdd.Size = UDim2.new(0.35, 0, 0, 30)
FovAdd.Position = UDim2.new(0.1, 0, 0, 225)
FovAdd.Text = "+"
FovAdd.MouseButton1Click:Connect(function() Settings.FOV = Settings.FOV + 10 FovLabel.Text = "FOV: "..Settings.FOV end)

local FovSub = Instance.new("TextButton", MainFrame)
FovSub.Size = UDim2.new(0.35, 0, 0, 30)
FovSub.Position = UDim2.new(0.55, 0, 0, 225)
FovSub.Text = "-"
FovSub.MouseButton1Click:Connect(function() Settings.FOV = Settings.FOV - 10 FovLabel.Text = "FOV: "..Settings.FOV end)

-- LÓGICA DE BUSCA DE ALVO
local function GetClosestPlayer()
    local Target = nil
    local Dist = Settings.FOV

    for _, v in pairs(Players:GetPlayers()) do
        if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild(Settings.AimPart) then
            if Settings.TeamCheck and v.Team == LocalPlayer.Team then continue end
            
            local Pos, OnScreen = Camera:WorldToViewportPoint(v.Character[Settings.AimPart].Position)
            if OnScreen then
                local MouseDist = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(Pos.X, Pos.Y)).Magnitude
                if MouseDist < Dist then
                    Target = v.Character[Settings.AimPart]
                    Dist = MouseDist
                end
            end
        end
    end
    return Target
end

-- LOOP DE EXECUÇÃO
RunService.RenderStepped:Connect(function()
    FOVCircle.Radius = Settings.FOV
    FOVCircle.Position = Vector2.new(Mouse.X, Mouse.Y + 36) -- Ajuste de offset da topbar
    FOVCircle.Visible = true

    if Settings.Aimbot then
        local Target = GetClosestPlayer()
        if Target then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, Target.Position)
        end
    end
end)

print("Script BHB carregado com sucesso!")
