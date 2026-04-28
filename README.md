-- BhBz Hub v7.5 - Stable & Fixed Edition
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
    MenuVisible = true, ESPColor = Color3.fromRGB(0, 255, 255), FOVColor = Color3.fromRGB(0, 255, 255),
    InfJump = false, AntiAfk = false, ClickTP = false, NoFog = false, AutoClick = false, XRay = false
}

-- INTERFACE
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "BhBz_Hub_v7_5"
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999
ScreenGui.IgnoreGuiInset = true

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 380, 0, 480)
MainFrame.Position = UDim2.new(0.5, -190, 0.5, -240)
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "BhBz Hub PRO v7.5"
Title.TextColor3 = Color3.fromRGB(0, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
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
    tab.Size = UDim2.new(1, 0, 1, 0); tab.BackgroundTransparency = 1; tab.Visible = false; tab.ScrollBarThickness = 3; tab.CanvasSize = UDim2.new(0,0,1.5,0)
end
Tabs.Combat.Visible = true

local function CreateTabBtn(name, pos, width)
    local btn = Instance.new("TextButton", TabButtons)
    btn.Size = UDim2.new(width, 0, 1, 0); btn.Position = pos; btn.Text = name
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40); btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.GothamBold; btn.TextSize = 12
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

-- COMBAT
AddToggle(Tabs.Combat, "Aimbot (M2)", 10, function(v) Settings.Aimbot = v end)
AddToggle(Tabs.Combat, "No Recoil", 50, function(v) Settings.NoRecoil = v end)
AddToggle(Tabs.Combat, "Auto Clicker", 90, function(v) Settings.AutoClick = v end)

-- VISUALS
AddToggle(Tabs.Visuals, "ESP Muralha", 10, function(v) Settings.ESP = v end)
AddToggle(Tabs.Visuals, "X-Ray", 50, function(v)
    Settings.XRay = v
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and not obj:Parent:FindFirstChildOfClass("Humanoid") then
            obj.LocalTransparencyModifier = v and 0.5 or 0
        end
    end
end)

-- PLAYER
AddToggle(Tabs.Player, "Infinite Jump", 10, function(v) Settings.InfJump = v end)
AddToggle(Tabs.Player, "NoClip", 50, function(v) Settings.NoClip = v end)
AddToggle(Tabs.Player, "Spinbot", 90, function(v) Settings.Spinbot = v end)

local function AddSlider(parent, name, yPos, callback)
    local label = Instance.new("TextLabel", parent)
    label.Size = UDim2.new(1,0,0,20); label.Position = UDim2.new(0,0,0,yPos); label.Text = name; label.TextColor3 = Color3.fromRGB(255,255,255); label.BackgroundTransparency = 1
    local b1 = Instance.new("TextButton", parent); b1.Size = UDim2.new(0.4,0,0,30); b1.Position = UDim2.new(0.05,0,0,yPos+20); b1.Text = "+"
    b1.MouseButton1Click:Connect(function() callback(true) end)
    local b2 = Instance.new("TextButton", parent); b2.Size = UDim2.new(0.4,0,0,30); b2.Position = UDim2.new(0.55,0,0,yPos+20); b2.Text = "-"
    b2.MouseButton1Click:Connect(function() callback(false) end)
end
AddSlider(Tabs.Player, "Speed", 140, function(a) Settings.WalkSpeed = a and Settings.WalkSpeed + 10 or math.max(16, Settings.WalkSpeed - 10) end)
AddSlider(Tabs.Player, "Jump", 200, function(a) Settings.JumpPower = a and Settings.JumpPower + 15 or math.max(50, Settings.JumpPower - 15) end)

-- SETTINGS
AddToggle(Tabs.Settings, "Full Bright", 10, function(v) Settings.FullBright = v end)
AddToggle(Tabs.Settings, "No Fog", 50, function(v) Settings.NoFog = v end)
AddToggle(Tabs.Settings, "Anti-AFK", 90, function(v) Settings.AntiAfk = v end)

-- LÓGICAS DE SISTEMA
UserInputService.JumpRequest:Connect(function()
    if Settings.InfJump and LocalPlayer.Character then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2; FOVCircle.Transparency = 0.8

RunService.RenderStepped:Connect(function()
    pcall(function()
        if Settings.NoFog then Lighting.FogEnd = 9e9 end
        if Settings.FullBright then Lighting.Brightness = 2; Lighting.ClockTime = 14; Lighting.GlobalShadows = false end
        Camera.FieldOfView = Settings.GameFOV
        
        -- FOV Circle
        FOVCircle.Visible = true; FOVCircle.Radius = Settings.FOV; FOVCircle.Color = Settings.FOVColor; FOVCircle.Position = UserInputService:GetMouseLocation()

        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        
        if hum and hrp then
            hum.WalkSpeed = Settings.WalkSpeed
            hum.JumpPower = Settings.JumpPower
            if Settings.Spinbot then
                hum.AutoRotate = false
                hrp.CFrame *= CFrame.Angles(0, math.rad(60), 0)
            else hum.AutoRotate = true end
            if Settings.NoClip then
                for _, p in pairs(char:GetDescendants()) do if p:IsA("BasePart") then p.CanCollide = false end end
            end
        end

        -- ESP & AIMBOT
        if Settings.ESP then
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= LocalPlayer and p.Character then
                    local h = p.Character:FindFirstChild("BhBz_ESP") or Instance.new("Highlight", p.Character)
                    h.Name = "BhBz_ESP"; h.FillColor = Settings.ESPColor
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
end)

-- Toggle Menu
UserInputService.InputBegan:Connect(function(i, gp)
    if not gp and i.KeyCode == Enum.KeyCode.LeftControl then
        MainFrame.Visible = not MainFrame.Visible
    end
end)

print("BhBz Hub v7.5 Loaded!")
