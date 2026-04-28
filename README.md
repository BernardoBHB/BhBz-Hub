-- BhBz Hub v10.0 - Redz Style Design
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
    FullBright = false,
    AimPart = "Head",
    FOV = 100,
    ESPColor = Color3.fromRGB(0, 255, 255),
    InfJump = false
}

-- INTERFACE PRINCIPAL
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BhBz_Redz_Style"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 500, 0, 320)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -160)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

-- Arredondar bordas do MainFrame
local MainCorner = Instance.new("UICorner", MainFrame)
MainCorner.CornerRadius = ToolToRadius and ToolToRadius(8) or UDim.new(0, 8)

-- Barra Lateral (Abas)
local SideBar = Instance.new("Frame", MainFrame)
SideBar.Size = UDim2.new(0, 130, 1, 0)
SideBar.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
SideBar.BorderSizePixel = 0

local SideCorner = Instance.new("UICorner", SideBar)
SideCorner.CornerRadius = UDim.new(0, 8)

local Title = Instance.new("TextLabel", SideBar)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "BhBz Hub"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.BackgroundTransparency = 1

-- Container de Conteúdo
local Content = Instance.new("Frame", MainFrame)
Content.Size = UDim2.new(1, -140, 1, -10)
Content.Position = UDim2.new(0, 135, 0, 5)
Content.BackgroundTransparency = 1

local Tabs = {
    Combat = Instance.new("ScrollingFrame", Content),
    Visual = Instance.new("ScrollingFrame", Content),
    Player = Instance.new("ScrollingFrame", Content)
}

for _, tab in pairs(Tabs) do
    tab.Size = UDim2.new(1, 0, 1, 0)
    tab.BackgroundTransparency = 1
    tab.Visible = false
    tab.ScrollBarThickness = 2
    tab.BorderSizePixel = 0
    tab.CanvasSize = UDim2.new(0, 0, 1.5, 0)
end
Tabs.Combat.Visible = true

-- Função para criar botões na lateral
local btnY = 50
local function CreateTabBtn(name, tabFrame)
    local btn = Instance.new("TextButton", SideBar)
    btn.Size = UDim2.new(0.9, 0, 0, 35)
    btn.Position = UDim2.new(0.05, 0, 0, btnY)
    btn.Text = name
    btn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    btn.TextColor3 = Color3.fromRGB(200, 200, 200)
    btn.Font = Enum.Font.GothamSemibold
    btn.TextSize = 13
    btn.AutoButtonColor = true
    
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 6)
    
    btn.MouseButton1Click:Connect(function()
        for _, t in pairs(Tabs) do t.Visible = false end
        tabFrame.Visible = true
    end)
    btnY = btnY + 40
end

CreateTabBtn("Combat", Tabs.Combat)
CreateTabBtn("Visual", Tabs.Visual)
CreateTabBtn("Player", Tabs.Player)

-- Função de Toggle estilo Redz
local function AddToggle(parent, name, yPos, callback)
    local frame = Instance.new("Frame", parent)
    frame.Size = UDim2.new(0.95, 0, 0, 40)
    frame.Position = UDim2.new(0, 5, 0, yPos)
    frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 6)
    
    local label = Instance.new("TextLabel", frame)
    label.Size = UDim2.new(1, -50, 1, 0)
    label.Position = UDim2.new(0, 10, 0, 0)
    label.Text = name
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.Font = Enum.Font.Gotham
    label.TextSize = 14
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.BackgroundTransparency = 1
    
    local btn = Instance.new("TextButton", frame)
    btn.Size = UDim2.new(0, 35, 0, 20)
    btn.Position = UDim2.new(1, -45, 0.5, -10)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.Text = ""
    
    local btnCorner = Instance.new("UICorner", btn)
    btnCorner.CornerRadius = UDim.new(1, 0)
    
    local dot = Instance.new("Frame", btn)
    dot.Size = UDim2.new(0, 14, 0, 14)
    dot.Position = UDim2.new(0, 3, 0.5, -7)
    dot.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
    Instance.new("UICorner", dot).CornerRadius = UDim.new(1, 0)
    
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        callback(enabled)
        if enabled then
            btn.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
            dot:TweenPosition(UDim2.new(1, -17, 0.5, -7), "Out", "Quad", 0.2)
        else
            btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
            dot:TweenPosition(UDim2.new(0, 3, 0.5, -7), "Out", "Quad", 0.2)
        end
    end)
end

-- ADICIONANDO FUNÇÕES
AddToggle(Tabs.Combat, "Aimbot (Hold M2)", 10, function(v) Settings.Aimbot = v end)
AddToggle(Tabs.Combat, "No Recoil", 55, function(v) Settings.NoRecoil = v end)

AddToggle(Tabs.Visual, "ESP Muralha", 10, function(v) Settings.ESP = v end)
AddToggle(Tabs.Visual, "Full Bright", 55, function(v) Settings.FullBright = v end)

AddToggle(Tabs.Player, "Velocidade (100)", 10, function(v) Settings.WalkSpeed = v and 100 or 16 end)
AddToggle(Tabs.Player, "Pulo Alto (150)", 55, function(v) Settings.JumpPower = v and 150 or 50 end)
AddToggle(Tabs.Player, "Infinite Jump", 100, function(v) Settings.InfJump = v end)
AddToggle(Tabs.Player, "NoClip", 145, function(v) Settings.NoClip = v end)
AddToggle(Tabs.Player, "Spinbot", 190, function(v) Settings.Spinbot = v end)

-- LÓGICAS (IGUAL AO v9.5)
local fov = Drawing.new("Circle")
fov.Thickness = 2; fov.Visible = true

local LastRotation = Camera.CFrame
RunService.RenderStepped:Connect(function()
    if Settings.NoRecoil and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
        local DeltaX, DeltaY, DeltaZ = Camera.CFrame:ToOrientation()
        local LastX = LastRotation:ToOrientation()
        if DeltaX > LastX then 
            Camera.CFrame = CFrame.new(Camera.CFrame.Position) * CFrame.fromOrientation(LastX, DeltaY, DeltaZ)
        end
    end
    LastRotation = Camera.CFrame
    
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

RunService.Heartbeat:Connect(function()
    pcall(function()
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if hum then
            hum.WalkSpeed = Settings.WalkSpeed
            hum.JumpPower = Settings.JumpPower
            if Settings.Spinbot then
                hum.AutoRotate = false; char.HumanoidRootPart.CFrame *= CFrame.Angles(0, math.rad(60), 0)
            else hum.AutoRotate = true end
            if Settings.NoClip then
                for _, p in pairs(char:GetDescendants()) do if p:IsA("BasePart") then p.CanCollide = false end end
            end
        end
        if Settings.FullBright then Lighting.Brightness = 2; Lighting.ClockTime = 14; Lighting.GlobalShadows = false end
        fov.Radius = Settings.FOV; fov.Color = Color3.fromRGB(255,255,255); fov.Position = UserInputService:GetMouseLocation()
    end)
end)

RunService.Stepped:Connect(function()
    if Settings.ESP then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local h = p.Character:FindFirstChild("Highlight") or Instance.new("Highlight", p.Character)
                h.Enabled = true; h.FillColor = Settings.ESPColor
            end
        end
    end
end)

UserInputService.JumpRequest:Connect(function()
    if Settings.InfJump and LocalPlayer.Character then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

UserInputService.InputBegan:Connect(function(i, gp)
    if not gp and i.KeyCode == Enum.KeyCode.LeftControl then MainFrame.Visible = not MainFrame.Visible end
end)
