-- BhBz Hub v2 - Edição Avançada
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES ATUALIZADAS
local Settings = {
    Aimbot = false,
    ESP = false,
    Spinbot = false,
    AimPart = "Head",
    FOV = 100,
    TeamCheck = false,
    MenuVisible = true,
    ESPColor = Color3.fromRGB(0, 255, 255),
    FOVColor = Color3.fromRGB(255, 255, 255)
}

-- INTERFACE DO MENU (Aumentada para 450px de altura)
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "BhBz_Hub"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 260, 0, 480)
MainFrame.Position = UDim2.new(0.5, -130, 0.5, -240)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "BhBz Hub PRO"
Title.TextColor3 = Color3.fromRGB(0, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20

-- Alternar Menu com Control
UserInputService.InputBegan:Connect(function(input, gp)
    if not gp and input.KeyCode == Enum.KeyCode.LeftControl then
        Settings.MenuVisible = not Settings.MenuVisible
        MainFrame.Visible = Settings.MenuVisible
    end
end)

-- FUNÇÃO PARA CRIAR BOTÕES
local function CreateButton(name, yPos, callback)
    local Btn = Instance.new("TextButton", MainFrame)
    Btn.Size = UDim2.new(0.85, 0, 0, 30)
    Btn.Position = UDim2.new(0.075, 0, 0, yPos)
    Btn.Text = name .. ": OFF"
    Btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    Btn.Font = Enum.Font.Gotham
    
    local enabled = false
    Btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        Btn.Text = name .. ": " .. (enabled and "ON" or "OFF")
        Btn.BackgroundColor3 = enabled and Color3.fromRGB(0, 150, 255) or Color3.fromRGB(40, 40, 40)
        callback(enabled)
    end)
end

-- --- ADICIONANDO AS FUNÇÕES ---

CreateButton("Aimbot", 50, function(v) Settings.Aimbot = v end)
CreateButton("ESP Muralha", 90, function(v) Settings.ESP = v end)
CreateButton("Spinbot", 130, function(v) Settings.Spinbot = v end)

-- Seletor Cabeça/Barriga
local PartBtn = Instance.new("TextButton", MainFrame)
PartBtn.Size = UDim2.new(0.85, 0, 0, 30)
PartBtn.Position = UDim2.new(0.075, 0, 0, 170)
PartBtn.Text = "Alvo: Cabeça"
PartBtn.BackgroundColor3 = Color3.fromRGB(60, 30, 100)
PartBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
PartBtn.MouseButton1Click:Connect(function()
    Settings.AimPart = (Settings.AimPart == "Head" and "HumanoidRootPart" or "Head")
    PartBtn.Text = "Alvo: " .. (Settings.AimPart == "Head" and "Cabeça" or "Barriga")
end)

-- AJUSTE DE FOV (Slider-like)
local FovLabel = Instance.new("TextLabel", MainFrame)
FovLabel.Size = UDim2.new(1, 0, 0, 20)
FovLabel.Position = UDim2.new(0, 0, 0, 210)
FovLabel.Text = "FOV: " .. Settings.FOV
FovLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
FovLabel.BackgroundTransparency = 1

local FovAdd = Instance.new("TextButton", MainFrame)
FovAdd.Text = "+"; FovAdd.Size = UDim2.new(0.4, 0, 0, 30); FovAdd.Position = UDim2.new(0.075, 0, 0, 230)
FovAdd.MouseButton1Click:Connect(function() Settings.FOV = Settings.FOV + 10 FovLabel.Text = "FOV: "..Settings.FOV end)

local FovSub = Instance.new("TextButton", MainFrame)
FovSub.Text = "-"; FovSub.Size = UDim2.new(0.4, 0, 0, 30); FovSub.Position = UDim2.new(0.525, 0, 0, 230)
FovSub.MouseButton1Click:Connect(function() Settings.FOV = math.max(10, Settings.FOV - 10) FovLabel.Text = "FOV: "..Settings.FOV end)

-- SELETORES DE CORES (Simples: Clica para mudar entre cores principais)
local colors = {Color3.fromRGB(0,255,255), Color3.fromRGB(255,0,0), Color3.fromRGB(0,255,0), Color3.fromRGB(255,255,0)}
local cIdx = 1

local ColorBtn = Instance.new("TextButton", MainFrame)
ColorBtn.Size = UDim2.new(0.85, 0, 0, 35)
ColorBtn.Position = UDim2.new(0.075, 0, 0, 280)
ColorBtn.Text = "Mudar Cor ESP/FOV"
ColorBtn.BackgroundColor3 = Settings.ESPColor
ColorBtn.MouseButton1Click:Connect(function()
    cIdx = (cIdx % #colors) + 1
    Settings.ESPColor = colors[cIdx]
    Settings.FOVColor = colors[cIdx]
    ColorBtn.BackgroundColor3 = colors[cIdx]
end)

-- FOV VISUAL
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2
FOVCircle.Transparency = 0.8

-- LÓGICA DO SPINBOT
RunService.Heartbeat:Connect(function()
    if Settings.Spinbot and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(50), 0)
    end
end)

-- LÓGICA DO ESP
RunService.RenderStepped:Connect(function()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character then
            local high = p.Character:FindFirstChild("BhBz_ESP")
            if Settings.ESP then
                if not high then
                    high = Instance.new("Highlight", p.Character)
                    high.Name = "BhBz_ESP"
                end
                high.FillColor = Settings.ESPColor
                high.Enabled = true
            elseif high then high.Enabled = false end
        end
    end
end)

-- LÓGICA DO AIMBOT
local function GetClosest()
    local t, dist = nil, Settings.FOV
    for _, v in pairs(Players:GetPlayers()) do
        if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild(Settings.AimPart) then
            local pos, screen = Camera:WorldToViewportPoint(v.Character[Settings.AimPart].Position)
            if screen then
                local mDist = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(pos.X, pos.Y)).Magnitude
                if mDist < dist then t = v.Character[Settings.AimPart]; dist = mDist end
            end
        end
    end
    return t
end

RunService.RenderStepped:Connect(function()
    FOVCircle.Radius = Settings.FOV
    FOVCircle.Color = Settings.FOVColor
    FOVCircle.Position = UserInputService:GetMouseLocation()
    FOVCircle.Visible = Settings.MenuVisible

    if Settings.Aimbot and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
        local target = GetClosest()
        if target then Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position) end
    end
end)
