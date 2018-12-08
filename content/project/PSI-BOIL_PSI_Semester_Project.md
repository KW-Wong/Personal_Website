+++
# Date this page was created.
date = 2016-04-27T00:00:00

# Project title.
title = "DNS of Molten Salt Bubble Growth with PSI-BOIL"

# Project summary to display on homepage.
summary = ""

# Optional image to display on homepage (relative to `static/img/` folder).
image_preview = ""

# Tags: can be used for filtering projects.
# Example: `tags = ["Wave Equation", "deep-learning"]`
tags = ["Bubble Growth", "Molten Salt", "Direct Numerical Simulation"]

# Optional external URL for project (replaces project detail page).
#url = "pdf/my-paper-name.pdf"

# Does the project detail page use math formatting?
math = true

# Optional featured image (relative to `static/img/` folder).
[header]
#image = "headers/bubbles-wide.jpg"
#caption = "My caption :smile:"


+++
This 3 month project was carried at the CFD Group, Laboratory of Scientific Computing and Modelling, Paul Scherrer Institute in Switzerland. 

The objective of the project is to test the capability of the PSI in-house CFD code, PSI-BOIL in simulating the phase change problem for the internally heated liquid. After a successful verification and the bubble growth simulation, the investigation would be further extended to consider the void distribution of the molten salt reactor fuel channel during accident transient. 

```matlab
# Example of code highlighting
%clear all variables in the workspace
clearvars;

% set the literals (hard-coded numbers used in the script)
Nx = 100;       % number of grid points for x dimension
Ny = 100;       % number of grid points for y dimension
dx = 1e-3;      % grid spacing (m) for x dimension
dy = 1e-3;      % grid spacing (m) for y dimension
c0 = 1500;      % sound speed (m/s)
Nt = 1000;        % number of time steps

% set the size of the time step to the stability limit, where in 1D the
% stability limit is given by dt <= dx / c0
dt = dx / c0/sqrt(2);

% set the initial pressure to be a gaussian
x=(1:Nx)*dx; y=(1:Ny)*dy; %x and y values between 1 and +100 spaced by 1,(100 values)
[X,Y]=meshgrid(x,y); % generate a 2D grid of xy values (100 by 100 arrays), X(i,j) = x(j) and Y(i,j) = y(i) 
p_n = exp(-( (X - (Nx/2)*dx).^2 + (Y - (Ny/2)*dy).^2) / (2*(2 * dx)^2)); 

%mesh(X,Y,p_n)

% set pressure at (n - 1) to be equal to n (this implicitly sets the
% initial particle velocity to be zero)
p_nm1 = p_n;

% preallocate the pressure at (n + 1) (this is updated during the time
% loop)
p_np1 = zeros(size(p_n));

% open a new figure window
figure;

% calculate pressure in a loop
for n = 1:Nt
    
    % -----------
    % CALCULATION
    % -----------
    
    % calculate the new value for the pressure
    p_np1(2:end-1,2:end-1) = 2*p_n(2:end-1,2:end-1) - p_nm1(2:end-1,2:end-1) + ...
        (c0*dt/dx)^2 * ( p_n(1:end-2,2:end-1) - 4*p_n(2:end-1,2:end-1) + ...
        p_n(3:end,2:end-1) + p_n(2:end-1,3:end)+ p_n(2:end-1,1:end-2));
    
    % copy the value at n to (n - 1)
    p_nm1 = p_n;
    
    % copy the pressure at (n + 1) to n
    p_n = p_np1;
    
    % -----------
    % PLOTTING
    % -----------
    
    % plot the pressure field
    mesh(X,Y,p_n)
    
    % set the limits on the y-axis
    set(gca, 'ZLim', [-0.25, 1]);
    
    % add a title with the iteration number
    title(['n = ' num2str(n)]);
    
    % force the plot to update
    drawnow;
    
    % briefly pause before continuing the loop
    pause(0.1)
        
end
```
