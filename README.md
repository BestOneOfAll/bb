local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local teleportDistance = 2
local teleporting = false
local teleportDuration = 4 -- duration of teleport in seconds

local function getClosestPlayerToMouse()
    local shortestDistance = math.huge
    local mouse = LocalPlayer:GetMouse()
    local closestPlayer = nil

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local playerPosition = player.Character.HumanoidRootPart.Position
            local mousePosition = Vector3.new(mouse.Hit.X, playerPosition.Y, mouse.Hit.Z)
            local distance = (playerPosition - mousePosition).magnitude
            if distance < shortestDistance then
                closestPlayer = player
                shortestDistance = distance
            end
        end
    end
    return closestPlayer
end

local function startTeleporting()
    teleporting = true
    local timeStarted = tick()
    local closestPlayer = getClosestPlayerToMouse()

    while teleporting and (tick() - timeStarted) < teleportDuration do
        if closestPlayer and closestPlayer.Character and closestPlayer.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            local targetPosition = closestPlayer.Character.HumanoidRootPart.Position - closestPlayer.Character.HumanoidRootPart.CFrame.lookVector * teleportDistance
            LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(targetPosition)
        end
        RunService.RenderStepped:Wait()
    end

    teleporting = false
end

local function onKeyPress(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.H and not teleporting then
        startTeleporting()
    end
end

local function onJump()
    teleporting = false
end

UserInputService.InputBegan:Connect(onKeyPress)
LocalPlayer.Character:WaitForChild("Humanoid").Jumping:Connect(onJump)
