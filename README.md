-- BhBz Hub v6.0 - Advanced No Recoil & Fixes
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES
local Settings = {
    Aimbot = false,
    NoRecoil = false,
    ESP = false,
    Spinbot = false,
    NoClip = false,
    WalkSpeed = 16,
    JumpPower = 50,
    Optimize = false,
    FullBright = false,
    GameFOV = 70,
    AimPart = "Head",
    FOV = 100,
    MenuVisible = true,
    ESPColor = Color3.fromRGB(0, 255, 255),
    FOVColor = Color3.fromRGB(0, 255, 255)
}

-- INTERFACE
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "BhBz_Hub_v6"
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999
ScreenGui.IgnoreGuiInset = true

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 350, 0, 450)
MainFrame.Position = UDim2.new(0.5, -175, 0.5, -225)
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "BhBz Hub PRO v6.0"
Title.TextColor3 = Color3.fromRGB(0, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18

-- SISTEMA DE ABAS
local TabButtons = Instance.new("Frame", MainFrame)
TabButtons.Size = UDim2.new(1, 0, 0, 30); TabButtons.Position = UDim2.new(0, 0, 0, 40)
TabButtons.BackgroundColor3 = Color3.fromRGB(30, 30, 30)

local Container = Instance.new("Frame", MainFrame)
Container.Size = UDim2.new(1, 0, 1, -70); Container.Position = UDim2.new(0, 0, 0, 70)
Container.BackgroundTransparency = 1

local Tabs = {
    Combat = Instance.new("ScrollingFrame", Container),
    Visuals = Instance.new("ScrollingFrame", Container),
    Player = Instance.new("ScrollingFrame", Container),
    Settings = Instance.new("ScrollingFrame", Container)
}

for _, tab in pairs(Tabs) do
    tab.Size = UDim2.new(1, 0, 1, 0); tab.BackgroundTransparency = 1; tab.Visible = false; tab.ScrollBarThickness = 2
end
Tabs.Combat.Visible = true

local function CreateTabBtn(name, pos, width)
    local btn = Instance.new("TextButton", TabButtons)
    btn.Size = UDim2.new(width, 0, 1, 0); btn.Position = pos; btn.Text = name
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40); btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.Gotham; btn.TextSize = 11
    btn.MouseButton1Click:Connect(function() 
        for n, f in pairs(Tabs) do f.Visible = (n == name) end 
    end)
end

CreateTabBtn("Combat", UDim2.new(0,0,0,0), 0.25)
CreateTabBtn("Visuals", UDim2.new(0.25,0,0,0), 0.25)
CreateTabBtn("Player", UDim2.new(0.5,0,0,0), 0.25)
CreateTabBtn("Settings", UDim2.new(0.75,0,0,0), 0.25)

local function AddToggle(parent, name, yPos, callback)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0.9, 0, 0, 35); btn.Position = UDim2.new(0.05, 0, 0, yPos); btn.Text = name .. ": OFF"
    btn.BackgroundColor3 = Color3.fromRGB(35, 35, 35); btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.Text = name .. ": " .. (enabled and "ON" or "OFF")
        btn.BackgroundColor3 = enabled and Color3.fromRGB(0, 180, 255) or Color3.fromRGB(35, 35, 35)
        callback(enabled)
    end)
end

-- --- CONTEÚDO COMBAT ---
AddToggle(Tabs.Combat, "Aimbot (M2)", 10, function(v) Settings.Aimbot = v end)
AddToggle(Tabs.Combat, "No Recoil Pro", 55, function(v) Settings.NoRecoil = v end)
local TgtBtn = Instance.new("TextButton", Tabs.Combat)
TgtBtn.Size = UDim2.new(0.9,0,0,35); TgtBtn.Position = UDim2.new(0.05,0,0,100); TgtBtn.Text = "Alvo: Cabeça"
TgtBtn.MouseButton1Click:Connect(function()
    Settings.AimPart = (Settings.AimPart == "Head" and "HumanoidRootPart" or "Head")
    TgtBtn.Text = "Alvo: " .. (Settings.AimPart == "Head" and "Cabeça" or "Barriga")
end)

-- --- CONTEÚDO VISUALS ---
AddToggle(Tabs.Visuals, "ESP Muralha", 10, function(v) Settings.ESP = v end)
local ColorBtn = Instance.new("TextButton", Tabs.Visuals)
ColorBtn.Size = UDim2.new(0.9,0,0,35); ColorBtn.Position = UDim2.new(0.05,0,0,55); ColorBtn.Text = "Mudar Cor do Hub"
local cols = {Color3.fromRGB(0,255,255), Color3.fromRGB(255,0,0), Color3.fromRGB(0,255,0), Color3.fromRGB(255,255,255)}
local ci = 1
ColorBtn.MouseButton1Click:Connect(function()
    ci = (ci % #cols) + 1; Settings.ESPColor = cols[ci]; Settings.FOVColor = cols[ci]; Title.TextColor3 = cols[ci]
end)

-- --- CONTEÚDO PLAYER ---
AddToggle(Tabs.Player, "NoClip", 10, function(v) Settings.NoClip = v end)
AddToggle(Tabs.Player, "Spinbot", 55, function(v) Settings.Spinbot = v end)

local function AddSlider(parent, name, yPos, callback)
    local label = Instance.new("TextLabel", parent)
    label.Size = UDim2.new(1,0,0,20); label.Position = UDim2.new(0,0,0,yPos); label.Text = name; label.TextColor3 = Color3.fromRGB(255,255,255); label.BackgroundTransparency = 1
    local b1 = Instance.new("TextButton", parent); b1.Size = UDim2.new(0.4,0,0,30); b1.Position = UDim2.new(0.05,0,0,yPos+20); b1.Text = "+"
    b1.MouseButton1Click:Connect(function() callback(true) end)
    local b2 = Instance.new("TextButton", parent); b2.Size = UDim2.new(0.4,0,0,30); b2.Position = UDim2.new(0.55,0,0,yPos+20); b2.Text = "-"
    b2.MouseButton1Click:Connect(function() callback(false) end)
end

AddSlider(Tabs.Player, "Speed", 100, function(a) Settings.WalkSpeed = a and Settings.WalkSpeed + 10 or math.max(16, Settings.WalkSpeed - 10) end)
AddSlider(Tabs.Player, "Jump", 160, function(a) Settings.JumpPower = a and Settings.JumpPower + 15 or math.max(50, Settings.JumpPower - 15) end)

-- --- LÓGICA NO RECOIL PROFISSIONAL ---
-- Esta lógica intercepta o tremor da câmera sem bugar o movimento do mouse
RunService.RenderStepped:Connect(function()
    if Settings.NoRecoil then
        local recoil = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
        if recoil then
            -- Para jogos com scripts de câmera padrão
            Camera.CFrame = Camera.CFrame * CFrame.Angles(0,0,0)
            -- Tenta localizar e anular variáveis de recoil comuns em scripts de armas
            for _, v in pairs(getgc(true)) do
                if type(v) == "table" and rawget(v, "Recoil") then
                    v.Recoil = 0
                end
            end
        end
    end
end)

-- LÓGICA DE ILUMINAÇÃO
RunService.Heartbeat:Connect(function()
    if Settings.FullBright then Lighting.Brightness = 2; Lighting.ClockTime = 14; Lighting.GlobalShadows = false end
    Camera.FieldOfView = Settings.GameFOV
    
    if Settings.NoClip then
        for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then part.CanCollide = false end
        end
    end
end)

-- LÓGICA PRINCIPAL
RunService.RenderStepped:Connect(function()
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.WalkSpeed = Settings.WalkSpeed; hum.JumpPower = Settings.JumpPower
        if Settings.Spinbot then 
            hum.AutoRotate = false; char.HumanoidRootPart.CFrame *= CFrame.Angles(0, math.rad(60), 0) 
        else hum.AutoRotate = true end
    end
    
    -- FOV e ESP
    local fovC = _G.FOVCircle or Drawing.new("Circle")
    _G.FOVCircle = fovC; fovC.Visible = true; fovC.Radius = Settings.FOV; fovC.Color = Settings.FOVColor
    fovC.Position = UserInputService:GetMouseLocation(); fovC.Thickness = 2
    
    if Settings.ESP then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local h = p.Character:FindFirstChild("BhBz_ESP") or Instance.new("Highlight", p.Character)
                h.Name = "BhBz_ESP"; h.FillColor = Settings.ESPColor; h.Enabled = true
            end
        end
    end

    -- AIMBOT
    if Settings.Aimbot and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
        local t, d = nil, Settings.FOV
        for _, v in pairs(Players:GetPlayers()) do
            if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild(Settings.AimPart) then
                local pos, on = Camera:WorldToViewportPoint(v.Character[Settings.AimPart].Position)
                if on then
                    local mD = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(pos.X, pos.Y)).Magnitude
                    if mD < d then t = v.Character[Settings.AimPart]; d = mD end
                end
            end
        end
        if t then Camera.CFrame = CFrame.new(Camera.CFrame.Position, t.Position) end
    end
end)

-- Abre/Fecha Menu
UserInputService.InputBegan:Connect(function(i, gp)
    if not gp and i.KeyCode == Enum.KeyCode.LeftControl then
        Settings.MenuVisible = not Settings.MenuVisible; MainFrame.Visible = Settings.MenuVisible
    end
end)
