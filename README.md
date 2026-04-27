-- BhBz Hub v3.1 - Spinbot Fixed Edition
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES
local Settings = {
    Aimbot = false,
    ESP = false,
    Spinbot = false,
    AimPart = "Head",
    FOV = 100,
    MenuVisible = true,
    ESPColor = Color3.fromRGB(0, 255, 255),
    FOVColor = Color3.fromRGB(0, 255, 255)
}

-- INTERFACE PRINCIPAL
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "BhBz_Hub_Tabs"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 300, 0, 400)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -200)
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
Title.TextSize = 18

-- SISTEMA DE ABAS
local TabButtons = Instance.new("Frame", MainFrame)
TabButtons.Size = UDim2.new(1, 0, 0, 30)
TabButtons.Position = UDim2.new(0, 0, 0, 40)
TabButtons.BackgroundColor3 = Color3.fromRGB(35, 35, 35)

local Container = Instance.new("Frame", MainFrame)
Container.Size = UDim2.new(1, 0, 1, -70)
Container.Position = UDim2.new(0, 0, 0, 70)
Container.BackgroundTransparency = 1

local Tabs = {
    Combat = Instance.new("ScrollingFrame", Container),
    Visuals = Instance.new("ScrollingFrame", Container),
    Misc = Instance.new("ScrollingFrame", Container)
}

for _, tab in pairs(Tabs) do
    tab.Size = UDim2.new(1, 0, 1, 0)
    tab.BackgroundTransparency = 1
    tab.Visible = false
    tab.ScrollBarThickness = 2
end
Tabs.Combat.Visible = true

local function OpenTab(tabName)
    for name, frame in pairs(Tabs) do frame.Visible = (name == tabName) end
end

local function CreateTabBtn(name, pos)
    local btn = Instance.new("TextButton", TabButtons)
    btn.Size = UDim2.new(0.33, 0, 1, 0); btn.Position = pos; btn.Text = name
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45); btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.Gotham; btn.MouseButton1Click:Connect(function() OpenTab(name) end)
end

CreateTabBtn("Combat", UDim2.new(0, 0, 0, 0))
CreateTabBtn("Visuals", UDim2.new(0.33, 0, 0, 0))
CreateTabBtn("Misc", UDim2.new(0.66, 0, 0, 0))

local function AddToggle(parent, name, yPos, callback)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0.9, 0, 0, 35); btn.Position = UDim2.new(0.05, 0, 0, yPos)
    btn.Text = name .. ": OFF"; btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.Text = name .. ": " .. (enabled and "ON" or "OFF")
        btn.BackgroundColor3 = enabled and Color3.fromRGB(0, 150, 255) or Color3.fromRGB(40, 40, 40)
        callback(enabled)
    end)
end

-- ABA COMBAT
AddToggle(Tabs.Combat, "Aimbot", 10, function(v) Settings.Aimbot = v end)
local TargetBtn = Instance.new("TextButton", Tabs.Combat)
TargetBtn.Size = UDim2.new(0.9, 0, 0, 35); TargetBtn.Position = UDim2.new(0.05, 0, 0, 55)
TargetBtn.Text = "Alvo: Cabeça"; TargetBtn.MouseButton1Click:Connect(function()
    Settings.AimPart = (Settings.AimPart == "Head" and "HumanoidRootPart" or "Head")
    TargetBtn.Text = "Alvo: " .. (Settings.AimPart == "Head" and "Cabeça" or "Barriga")
end)

-- ABA VISUALS
AddToggle(Tabs.Visuals, "ESP Muralha", 10, function(v) Settings.ESP = v end)
local FovLabel = Instance.new("TextLabel", Tabs.Visuals)
FovLabel.Size = UDim2.new(1, 0, 0, 20); FovLabel.Position = UDim2.new(0, 0, 0, 55); FovLabel.TextColor3 = Color3.fromRGB(255,255,255); FovLabel.BackgroundTransparency = 1; FovLabel.Text = "Ajuste FOV: " .. Settings.FOV

local FovPlus = Instance.new("TextButton", Tabs.Visuals)
FovPlus.Size = UDim2.new(0.45, 0, 0, 30); FovPlus.Position = UDim2.new(0.05, 0, 0, 80); FovPlus.Text = "+"
FovPlus.MouseButton1Click:Connect(function() Settings.FOV = Settings.FOV + 10; FovLabel.Text = "Ajuste FOV: "..Settings.FOV end)

local FovMinus = Instance.new("TextButton", Tabs.Visuals)
FovMinus.Size = UDim2.new(0.45, 0, 0, 30); FovMinus.Position = UDim2.new(0.5, 0, 0, 80); FovMinus.Text = "-"
FovMinus.MouseButton1Click:Connect(function() Settings.FOV = math.max(10, Settings.FOV - 10); FovLabel.Text = "Ajuste FOV: "..Settings.FOV end)

-- ABA MISC
AddToggle(Tabs.Misc, "Spinbot", 10, function(v) Settings.Spinbot = v end)
local ColorBtn = Instance.new("TextButton", Tabs.Misc)
ColorBtn.Size = UDim2.new(0.9, 0, 0, 35); ColorBtn.Position = UDim2.new(0.05, 0, 0, 55); ColorBtn.Text = "Mudar Cor"; ColorBtn.BackgroundColor3 = Settings.ESPColor
local cols = {Color3.fromRGB(0,255,255), Color3.fromRGB(255,0,0), Color3.fromRGB(0,255,0), Color3.fromRGB(255,255,0)}
local cI = 1
ColorBtn.MouseButton1Click:Connect(function()
    cI = (cI % #cols) + 1
    Settings.ESPColor = cols[cI]; Settings.FOVColor = cols[cI]; ColorBtn.BackgroundColor3 = cols[cI]
end)

-- LÓGICAS
UserInputService.InputBegan:Connect(function(i, gp)
    if not gp and i.KeyCode == Enum.KeyCode.LeftControl then
        Settings.MenuVisible = not Settings.MenuVisible; MainFrame.Visible = Settings.MenuVisible
    end
end)

local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2; FOVCircle.Transparency = 0.8

-- MOTOR DO SPINBOT CORRIGIDO
RunService.Stepped:Connect(function()
    if Settings.Spinbot then
        local character = LocalPlayer.Character
        local humanoid = character and character:FindFirstChildOfClass("Humanoid")
        local hrp = character and character:FindFirstChild("HumanoidRootPart")
        
        if hrp and humanoid then
            humanoid.AutoRotate = false -- Desativa a rotação automática do jogo
            hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(60), 0) -- Gira 60 graus por frame
        end
    elseif LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid").AutoRotate = true
    end
end)

RunService.RenderStepped:Connect(function()
    FOVCircle.Radius = Settings.FOV; FOVCircle.Color = Settings.FOVColor; FOVCircle.Position = UserInputService:GetMouseLocation(); FOVCircle.Visible = Settings.MenuVisible
    
    -- ESP
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character then
            local high = p.Character:FindFirstChild("BhBz_ESP")
            if Settings.ESP then
                if not high then high = Instance.new("Highlight", p.Character); high.Name = "BhBz_ESP" end
                high.FillColor = Settings.ESPColor; high.Enabled = true
            elseif high then high.Enabled = false end
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
