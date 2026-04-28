-- BhBz Hub v8.0 - Final Fix
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES
local Settings = {
    Aimbot = false, NoRecoil = false, ESP = false, Spinbot = false,
    NoClip = false, WalkSpeed = 16, JumpPower = 50, Optimize = false,
    FullBright = false, GameFOV = 70, AimPart = "Head", FOV = 100,
    ESPColor = Color3.fromRGB(0, 255, 255), FOVColor = Color3.fromRGB(0, 255, 255),
    InfJump = false, AntiAfk = false, NoFog = false
}

-- CRIAR GUI (Protegido)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BhBz_Hub_v8"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 350, 0, 400)
MainFrame.Position = UDim2.new(0.5, -175, 0.5, -200)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "BhBz Hub PRO v8.0"
Title.TextColor3 = Color3.fromRGB(0, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
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
    Player = Instance.new("ScrollingFrame", Container)
}

for _, tab in pairs(Tabs) do
    tab.Size = UDim2.new(1, 0, 1, 0); tab.BackgroundTransparency = 1; tab.Visible = false
    tab.ScrollBarThickness = 3; tab.CanvasSize = UDim2.new(0, 0, 2, 0)
end
Tabs.Combat.Visible = true

local function CreateTabBtn(name, pos, width)
    local btn = Instance.new("TextButton", TabButtons)
    btn.Size = UDim2.new(width, 0, 1, 0); btn.Position = pos; btn.Text = name
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40); btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.GothamBold; btn.TextSize = 11
    btn.MouseButton1Click:Connect(function() 
        for n, f in pairs(Tabs) do f.Visible = (n == name) end 
    end)
end

CreateTabBtn("Combat", UDim2.new(0,0,0,0), 0.33)
CreateTabBtn("Visuals", UDim2.new(0.33,0,0,0), 0.33)
CreateTabBtn("Player", UDim2.new(0.66,0,0,0), 0.34)

local function AddToggle(parent, name, yPos, callback)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0.9, 0, 0, 30); btn.Position = UDim2.new(0.05, 0, 0, yPos)
    btn.Text = name .. ": OFF"; btn.BackgroundColor3 = Color3.fromRGB(35, 35, 35); btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.Text = name .. ": " .. (enabled and "ON" or "OFF")
        btn.BackgroundColor3 = enabled and Color3.fromRGB(0, 180, 255) or Color3.fromRGB(35, 35, 35)
        callback(enabled)
    end)
end

-- BOTOES
AddToggle(Tabs.Combat, "Aimbot (M2)", 10, function(v) Settings.Aimbot = v end)
AddToggle(Tabs.Combat, "No Recoil", 50, function(v) Settings.NoRecoil = v end)
AddToggle(Tabs.Visuals, "ESP Muralha", 10, function(v) Settings.ESP = v end)
AddToggle(Tabs.Visuals, "Full Bright", 50, function(v) Settings.FullBright = v end)
AddToggle(Tabs.Player, "Speed + Jump", 10, function(v) Settings.WalkSpeed = v and 100 or 16; Settings.JumpPower = v and 100 or 50 end)
AddToggle(Tabs.Player, "Infinite Jump", 50, function(v) Settings.InfJump = v end)
AddToggle(Tabs.Player, "NoClip", 90, function(v) Settings.NoClip = v end)

-- FOV CIRCLE (Protegido)
local fov = Drawing.new("Circle")
fov.Thickness = 2
fov.Transparency = 0.8

-- LOGICA INFINITE JUMP
UserInputService.JumpRequest:Connect(function()
    if Settings.InfJump and LocalPlayer.Character then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

-- LOOP PRINCIPAL (Optimizado)
RunService.Heartbeat:Connect(function()
    pcall(function()
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        local hrp = char and char:FindFirstChild("HumanoidRootPart")

        -- FOV
        fov.Visible = true
        fov.Radius = Settings.FOV
        fov.Color = Settings.FOVColor
        fov.Position = UserInputService:GetMouseLocation()

        if hum and hrp then
            hum.WalkSpeed = Settings.WalkSpeed
            hum.JumpPower = Settings.JumpPower
            
            if Settings.NoClip then
                for _, p in pairs(char:GetDescendants()) do
                    if p:IsA("BasePart") then p.CanCollide = false end
                end
            end
        end

        -- ESP
        if Settings.ESP then
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= LocalPlayer and p.Character then
                    local h = p.Character:FindFirstChild("Highlight") or Instance.new("Highlight", p.Character)
                    h.FillColor = Settings.ESPColor
                end
            end
        end

        -- AIMBOT
        if Settings.Aimbot and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
            local target = nil
            local dist = Settings.FOV
            for _, v in pairs(Players:GetPlayers()) do
                if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("Head") then
                    local pos, on = Camera:WorldToViewportPoint(v.Character.Head.Position)
                    if on then
                        local mD = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(pos.X, pos.Y)).Magnitude
                        if mD < dist then target = v.Character.Head; dist = mD end
                    end
                end
            end
            if target then Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position) end
        end
    end)
end)

-- FECHAR/ABRIR (Control)
UserInputService.InputBegan:Connect(function(i, gp)
    if not gp and i.KeyCode == Enum.KeyCode.LeftControl then
        MainFrame.Visible = not MainFrame.Visible
    end
end)

print("BhBz Hub v8 Executado com Sucesso!")
