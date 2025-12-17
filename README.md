local Players = game:GetService("Players")
local RS = game:GetService("RunService")
local player = Players.LocalPlayer
local car = nil

repeat wait() until workspace:FindFirstChild("CarContainer")
car = workspace.CarContainer:WaitForChild(player.Name.."_Car")

--// GUI
local Screen = Instance.new("ScreenGui", game:GetService("CoreGui"))
local Main = Instance.new("Frame", Screen)
Main.Size = UDim2.new(0,220,0,130)
Main.Position = UDim2(0.5,-110,0.3,0)
Main.BackgroundColor3 = Color3.fromRGB(30,30,30)
Main.Active, Main.Draggable = true, true

local title = Instance.new("TextLabel", Main)
title.Size = UDim2(1,0,0,25)
title.Text = "Formula 2026 – Distance Farm"
title.BackgroundColor3 = Color3.fromRGB(90,0,90)

local farmBtn = Instance.new("TextButton", Main)
farmBtn.Size = UDim2(1,-10,0,30)
farmBtn.Position = UDim2(0,5,0,30)
farmBtn.Text = "Start Farm"
farmBtn.BackgroundColor3 = Color3.new(0.2,0.2,0.2)

local spdBtn = Instance.new("TextButton", Main)
spdBtn.Size = UDim2(1,-10,0,25)
spdBtn.Position = UDim2(0,5,0,65)
spdBtn.Text = "Speed 3×"

local gitBtn = Instance.new("TextButton", Main)
gitBtn.Size = UDim2(1,-10,0,25)
gitBtn.Position = UDim2(0,5,0,95)
gitBtn.Text = "GitHub README"
gitBtn.BackgroundColor3 = Color3.fromRGB(0,100,200)

--// Lógica
local farming = false
local track = workspace:WaitForChild("Track")
local waypoints = {} -- pontos do centro da pista
for _,p in ipairs(track:GetDescendants()) do
    if p.Name:find("WP") or p.Name:find("Node") then table.insert(waypoints, p) end
end
if #waypoints == 0 then -- fallback
    for i=1,36 do table.insert(waypoints, CFrame.new(Vector3.new(200*math.cos(math.rad(i*10)), 5.y, 200*math.sin(math.rad(i*10))))) end
end

local idx, dir = 1, 1

local function farmStep()
    if not farming then return end
    local target = waypoints[idx]
    local cf = typeof(target)=="Instance" and target.CFrame orFrame or target
    -- mantém carro alinhado e em movimento
    car:SetPrimaryPartCFrame(cf)
    car.PrimaryPart.Velocity = cf.lookVector * (car.DriveSeat.MaxSpeed * dir)
    idx = idx + dir
    if idx > #waypoints then idx, dir = #waypoints, -1 end
    if idx < 1 then idx, dir = 1, 1 end
end

farmBtn.MouseButton1Click:Connect(function()
    farming = not farming
    farmBtn.Text = farming and "Farming..." or "Start Farm"
    farmBtn.BackgroundColor3 = farming and Color3.new(0,0.7,0) or Color3.new(0.2,0.2,0.2)
end)

spdBtn.MouseButton1Click:Connect(function()
    car.DriveSeat.MaxSpeed = (car.DriveSeat.MaxSpeed > 80000) and 600 or 1800
    spdBtn.Text = (car.DriveSeat.MaxSpeed > 100) and "Speed 1×" or "Speed 3×"
end)

gitBtn.MouseButton1Click:Connect(function()
    game:GetService("TeleportService"):Teleport(0, player) -- dummy
    writefunction("https://github.com/lobao007ps1/asdasd/edit/main/README.md")
end)

RS.Heartbeat:Connect(farmStep)
