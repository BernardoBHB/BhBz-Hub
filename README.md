-- BhBz Hub v9.0 - Lite & Fixed
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES (Limpas e Funcionais)
local Settings = {
    Aimbot = false,
    NoRecoil = false,
    ESP = false,
    Spinbot = false,
    NoClip = false,
    WalkSpeed = 16,
    JumpPower = 50,
    FullBright = false,
    GameFOV = 70,
    AimPart = "Head",
    FOV = 100,
    ESPColor = Color3.fromRGB(0, 255, 255),
    FOVColor = Color3.fromRGB(0, 255, 255),
    InfJump = false
}

-- INTERFACE (Protegida)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BhBz_Hub_v9"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 350, 0, 380)
MainFrame.Position = UDim2.new(0.5, -175, 0.5, -190)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "BhBz Hub PRO v9.0"
Title.TextColor3 = Color3.fromRGB(0, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18

-- ABAS
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
    tab.ScrollBarThickness = 3; tab.CanvasSize = UDim2.new(0, 0, 1.5, 0)
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

-- BOTOES COMBAT
AddToggle(Tabs.Combat, "Aimbot (M2)", 10, function(v) Settings.Aimbot = v end)
AddToggle(Tabs.Combat, "No Recoil", 50, function(v) Settings.NoRecoil = v end)

-- BOTOES VISUALS
AddToggle(Tabs.Visuals, "ESP Muralha", 10, function(v) Settings.ESP = v end)
AddToggle(Tabs.Visuals, "Full Bright", 50, function(v) Settings.FullBright = v end)

-- BOTOES PLAYER
AddToggle(Tabs.Player, "Velocidade (100)", 10, function(v) Settings.WalkSpeed = v and 100 or 16 end)
AddToggle(Tabs.Player, "Pulo Alto (150)", 50, function(v) Settings.JumpPower = v and 150 or 50 end)
AddToggle(Tabs.Player, "Infinite Jump", 90, function(v) Settings.InfJump = v end)
AddToggle(Tabs.Player, "NoClip", 130, function(v) Settings.NoClip = v end)
AddToggle(Tabs.Player, "Spinbot", 170, function(v) Settings.Spinbot = v end)

-- FOV CIRCLE
local fov = Drawing.new("Circle")
fov.Thickness = 2; fov.Transparency = 0.8; fov.Visible = true

-- INFINITE JUMP
UserInputService.JumpRequest:Connect(function()
    if Settings.InfJump and LocalPlayer.Character then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

-- LOOP PRINCIPAL
RunService.Heartbeat:Connect(function()
    pcall(function()
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        local hrp = char and char:FindFirstChild("HumanoidRootPart")

        -- FOV
        fov.Radius = Settings.FOV; fov.Color = Settings.FOVColor; fov.Position = UserInputService:GetMouseLocation()

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
    end)
end)

-- RENDER (ESP E AIMBOT)
RunService.RenderStepped:Connect(function()
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

-- Toggle Menu (Control)
UserInputService.InputBegan:Connect(function(i, gp)
    if not gp and i.KeyCode == Enum.KeyCode.LeftControl then MainFrame.Visible = not MainFrame.Visible end
end)

print("BhBz Hub v9.0 Executado!")
