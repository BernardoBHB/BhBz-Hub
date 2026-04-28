-- BhBz Hub v8.5 - Complete & Stable
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES COMPLETAS
local Settings = {
    Aimbot = false, NoRecoil = false, ESP = false, Spinbot = false,
    NoClip = false, WalkSpeed = 16, JumpPower = 50, Optimize = false,
    FullBright = false, GameFOV = 70, AimPart = "Head", FOV = 100,
    ESPColor = Color3.fromRGB(0, 255, 255), FOVColor = Color3.fromRGB(0, 255, 255),
    InfJump = false, AntiAfk = false, NoFog = false, AutoClick = false, XRay = false
}

-- INTERFACE (DisplayOrder alto para sobrepor tudo)
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "BhBz_Hub_v8_5"
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 380, 0, 450)
MainFrame.Position = UDim2.new(0.5, -190, 0.5, -225)
MainFrame.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "BhBz Hub PRO v8.5"
Title.TextColor3 = Color3.fromRGB(0, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20

-- SISTEMA DE ABAS
local TabButtons = Instance.new("Frame", MainFrame)
TabButtons.Size = UDim2.new(1, 0, 0, 35); TabButtons.Position = UDim2.new(0, 0, 0, 40)
TabButtons.BackgroundColor3 = Color3.fromRGB(30, 30, 30)

local Container = Instance.new("Frame", MainFrame)
Container.Size = UDim2.new(1, 0, 1, -75); Container.Position = UDim2.new(0, 0, 0, 75)
Container.BackgroundTransparency = 1

local Tabs = {
    Combat = Instance.new("ScrollingFrame", Container),
    Visuals = Instance.new("ScrollingFrame", Container),
    Player = Instance.new("ScrollingFrame", Container),
    Settings = Instance.new("ScrollingFrame", Container)
}

for _, tab in pairs(Tabs) do
    tab.Size = UDim2.new(1, 0, 1, 0); tab.BackgroundTransparency = 1; tab.Visible = false
    tab.ScrollBarThickness = 3; tab.CanvasSize = UDim2.new(0, 0, 1.8, 0)
end
Tabs.Combat.Visible = true

local function CreateTabBtn(name, pos, width)
    local btn = Instance.new("TextButton", TabButtons)
    btn.Size = UDim2.new(width, 0, 1, 0); btn.Position = pos; btn.Text = name
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45); btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.GothamBold; btn.TextSize = 11
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
    btn.Size = UDim2.new(0.9, 0, 0, 35); btn.Position = UDim2.new(0.05, 0, 0, yPos)
    btn.Text = name .. ": OFF"; btn.BackgroundColor3 = Color3.fromRGB(35, 35, 35); btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.Text = name .. ": " .. (enabled and "ON" or "OFF")
        btn.BackgroundColor3 = enabled and Color3.fromRGB(0, 180, 255) or Color3.fromRGB(35, 35, 35)
        callback(enabled)
    end)
end

-- COMBAT
AddToggle(Tabs.Combat, "Aimbot (M2)", 10, function(v) Settings.Aimbot = v end)
AddToggle(Tabs.Combat, "No Recoil Pro", 55, function(v) Settings.NoRecoil = v end)
AddToggle(Tabs.Combat, "Auto Clicker", 100, function(v) Settings.AutoClick = v end)

-- VISUALS
AddToggle(Tabs.Visuals, "ESP Muralha", 10, function(v) Settings.ESP = v end)
AddToggle(Tabs.Visuals, "X-Ray", 55, function(v)
    Settings.XRay = v
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and not obj:Parent:FindFirstChildOfClass("Humanoid") then
            obj.LocalTransparencyModifier = v and 0.5 or 0
        end
    end
end)
local ColorBtn = Instance.new("TextButton", Tabs.Visuals)
ColorBtn.Size = UDim2.new(0.9,0,0,35); ColorBtn.Position = UDim2.new(0.05,0,0,100); ColorBtn.Text = "Mudar Cor do Hub"
ColorBtn.MouseButton1Click:Connect(function()
    local cols = {Color3.fromRGB(0,255,255), Color3.fromRGB(255,0,0), Color3.fromRGB(0,255,0), Color3.fromRGB(255,255,255)}
    Settings.ESPColor = cols[math.random(1, #cols)]; Settings.FOVColor = Settings.ESPColor
    ColorBtn.BackgroundColor3 = Settings.ESPColor; Title.TextColor3 = Settings.ESPColor
end)

-- PLAYER
AddToggle(Tabs.Player, "Speed + Jump (Super)", 10, function(v) Settings.WalkSpeed = v and 120 or 16; Settings.JumpPower = v and 150 or 50 end)
AddToggle(Tabs.Player, "Infinite Jump", 55, function(v) Settings.InfJump = v end)
AddToggle(Tabs.Player, "NoClip", 100, function(v) Settings.NoClip = v end)
AddToggle(Tabs.Player, "Spinbot", 145, function(v) Settings.Spinbot = v end)

-- SETTINGS
AddToggle(Tabs.Settings, "Full Bright", 10, function(v) Settings.FullBright = v end)
AddToggle(Tabs.Settings, "No Fog", 55, function(v) Settings.NoFog = v end)
AddToggle(Tabs.Settings, "Anti-AFK", 100, function(v) Settings.AntiAfk = v end)
AddToggle(Tabs.Settings, "Optimize Map (FPS)", 145, function(v)
    if v then
        for _, x in pairs(workspace:GetDescendants()) do
            if x:IsA("BasePart") then x.Material = Enum.Material.SmoothPlastic
            elseif x:IsA("Decal") or x:IsA("Texture") then x.Transparency = 1 end
        end
    end
end)

-- FOV CIRCLE
local fov = Drawing.new("Circle")
fov.Thickness = 2; fov.Transparency = 0.8; fov.Visible = true

-- LÓGICAS (SISTEMA)
LocalPlayer.Idled:Connect(function()
    if Settings.AntiAfk then game:GetService("VirtualUser"):Button2Down(Vector2.new(0,0), Camera.CFrame) end
end)

UserInputService.JumpRequest:Connect(function()
    if Settings.InfJump and LocalPlayer.Character then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

RunService.Heartbeat:Connect(function()
    pcall(function()
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        local hrp = char and char:FindFirstChild("HumanoidRootPart")

        if hum and hrp then
            hum.WalkSpeed = Settings.WalkSpeed
            hum.JumpPower = Settings.JumpPower
            if Settings.Spinbot then
                hum.AutoRotate = false; hrp.CFrame *= CFrame.Angles(0, math.rad(60), 0)
            else hum.AutoRotate = true end
            if Settings.NoClip then
                for _, p in pairs(char:GetDescendants()) do if p:IsA("BasePart") then p.CanCollide = false end end
            end
        end

        if Settings.FullBright then Lighting.Brightness = 2; Lighting.ClockTime = 14; Lighting.GlobalShadows = false end
        if Settings.NoFog then Lighting.FogEnd = 9e9 end
        if Settings.AutoClick then mouse1click() end
    end)
end)

RunService.RenderStepped:Connect(function()
    fov.Radius = Settings.FOV; fov.Color = Settings.FOVColor; fov.Position = UserInputService:GetMouseLocation()
    
    if Settings.ESP then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local h = p.Character:FindFirstChild("Highlight") or Instance.new("Highlight", p.Character)
                h.FillColor = Settings.ESPColor
            end
        end
    end

    if Settings.Aimbot and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
        local target = nil; local dist = Settings.FOV
        for _, v in pairs(Players:GetPlayers()) do
            if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild(Settings.AimPart) then
                local pos, on = Camera:WorldToViewportPoint(v.Character[Settings.AimPart].Position)
                if on then
                    local mD = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(pos.X, pos.Y)).Magnitude
                    if mD < dist then target = v.Character[Settings.AimPart]; dist = mD end
                end
            end
        end
        if target then Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position) end
    end
end)

UserInputService.InputBegan:Connect(function(i, gp)
    if not gp and i.KeyCode == Enum.KeyCode.LeftControl then MainFrame.Visible = not MainFrame.Visible end
end)

print("BhBz Hub v8.5 Carregado com Sucesso!")
